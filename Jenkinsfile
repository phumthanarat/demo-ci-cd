pipeline {
    agent none

    environment {
        IMAGE = "phumthanarat/demo-ci-cd"
        TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Build & Push') {
            agent {
                kubernetes {
                    label 'docker-agent'
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
                    withCredentials([usernamePassword(
                        credentialsId: 'dockerhub-token',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )]) {

                        sh '''
                            echo "=== Docker Version ==="
                            docker version

                            echo "=== Login ==="
                            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin

                            echo "=== Build ==="
                            docker build -t $IMAGE:$TAG .
                            docker tag $IMAGE:$TAG $IMAGE:latest

                            echo "=== Push ==="
                            docker push $IMAGE:$TAG
                            docker push $IMAGE:latest
                        '''
                    }
                }
            }
        }

        stage('Deploy') {
            agent any

            steps {
                sh '''
                    kubectl apply -f k8s/
                    kubectl rollout status deployment/demo-ci-cd
                '''
            }
        }
    }
}
