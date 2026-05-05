pipeline {
    agent any

    environment {
        IMAGE_NAME = "phumthanarat/demo-ci-cd"
        TAG = "${BUILD_NUMBER}"
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
    command:
    - cat
    tty: true
    volumeMounts:
    - name: docker-config
      mountPath: /kaniko/.docker
  volumes:
  - name: docker-config
    secret:
      secretName: dockerhub-creds
"""
                }
            }

            steps {
                container('kaniko') {
                    sh """
                    /kaniko/executor \
                      --dockerfile=Dockerfile \
                      --context=$(pwd) \
                      --destination=${IMAGE_NAME}:${TAG} \
                      --destination=${IMAGE_NAME}:latest \
                      --skip-tls-verify
                    """
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
    image: bitnami/kubectl:latest
    command:
    - cat
    tty: true
"""
                }
            }

            steps {
                container('kubectl') {
                    sh """
                    kubectl apply -f k8s/
                    kubectl set image deployment/demo-ci-cd demo-ci-cd=${IMAGE_NAME}:${TAG} -n default
                    """
                }
            }
        }
    }
}