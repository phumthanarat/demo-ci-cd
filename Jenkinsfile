pipeline {
    agent any

    environment {
        IMAGE_NAME = "phumthanarat/demo-ci-cd"
        TAG = "${BUILD_NUMBER}"
        REGISTRY = "docker.io"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Push (Kaniko)') {
            agent {
                kubernetes {
                    yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:latest
    args:
      - "--dockerfile=Dockerfile"
      - "--context=dir://."
      - "--destination=$IMAGE_NAME:$TAG"
      - "--destination=$IMAGE_NAME:latest"
    volumeMounts:
    - name: docker-config
      mountPath: /kaniko/.docker
  restartPolicy: Never
  volumes:
  - name: docker-config
    secret:
      secretName: dockerhub-creds
"""
                }
            }

            steps {
                container('kaniko') {
                    sh "echo Building and pushing image..."
                }
            }
        }

        stage('Deploy') {
            agent {
                kubernetes {
                    yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: kubectl
    image: bitnami/kubectl:1.29.5
    command: ["sleep"]
    args: ["infinity"]
    tty: true
"""
                }
            }

            steps {
                container('kubectl') {
                    sh """
                    kubectl apply -f k8s/

                    kubectl set image deployment/demo-ci-cd \
                        demo-ci-cd=$IMAGE_NAME:$TAG

                    kubectl rollout status deployment/demo-ci-cd --timeout=120s
                    """
                }
            }
        }
    }
}