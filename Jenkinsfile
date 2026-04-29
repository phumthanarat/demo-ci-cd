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

        stage('Build & Push') {
            agent {
                kubernetes {
                    yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: docker
    image: docker:26-cli
    command:
    - cat
    tty: true
    volumeMounts:
    - name: dockersock
      mountPath: /var/run/docker.sock
  volumes:
  - name: dockersock
    hostPath:
      path: /var/run/docker.sock
"""
                }
            }

            steps {
                container('docker') {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS')]) {

                        sh '''
                        echo $PASS | docker login -u $USER --password-stdin
                        docker build -t $IMAGE_NAME:$TAG .
                        docker push $IMAGE_NAME:$TAG
                        docker tag $IMAGE_NAME:$TAG $IMAGE_NAME:latest
                        docker push $IMAGE_NAME:latest
                        '''
                    }
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
                    kubectl set image deployment/demo-ci-cd demo-ci-cd=$IMAGE_NAME:$TAG
                    """
                }
            }
        }
    }
}