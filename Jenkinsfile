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
                    defaultContainer 'docker'
                    yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: docker
    image: docker:26-cli
    command: ["cat"]
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
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )]) {

                        sh '''
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin

                            docker build -t $IMAGE:$TAG .
                            docker tag $IMAGE:$TAG $IMAGE:latest

                            docker push $IMAGE:$TAG
                            docker push $IMAGE:latest
                        '''
                    }
                }
            }
        }

        stage('Deploy') {
            agent {
                kubernetes {
                    label 'kubectl-agent'
                    defaultContainer 'kubectl'
                    yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: kubectl
    image: bitnami/kubectl:1.30
    command: ["cat"]
    tty: true
"""
                }
            }

            steps {
                container('kubectl') {
                    sh '''
                        kubectl version --client
                        kubectl get nodes

                        kubectl apply -f k8s/
                        kubectl rollout status deployment/demo-ci-cd
                    '''
                }
            }
        }
    }
}
