pipeline {
    agent any

    environment {
        IMAGE = "phumthanarat/demo-ci-cd:${BUILD_NUMBER}"
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
                    defaultContainer 'docker'
                    yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: docker
    image: docker:26-cli
    command: ['cat']
    tty: true
    volumeMounts:
    - name: dockersock
      mountPath: /var/run/docker.sock

  - name: jnlp
    image: jenkins/inbound-agent:latest

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
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS'
                    )]) {

                        sh """
                            echo $PASS | docker login -u $USER --password-stdin

                            docker build -t $IMAGE .
                            docker push $IMAGE
                            docker tag $IMAGE phumthanarat/demo-ci-cd:latest
                            docker push phumthanarat/demo-ci-cd:latest
                        """
                    }
                }
            }
        }

        stage('Deploy') {
            agent {
                kubernetes {
                    containerTemplate(
                        name: 'kubectl',
                        image: 'bitnami/kubectl:1.29',
                        command: 'cat',
                        ttyEnabled: true
                    )
                }
            }

            steps {
                container('kubectl') {
                    sh """
                        kubectl apply -f k8s/
                        kubectl set image deployment/demo-ci-cd demo-ci-cd=$IMAGE
                    """
                }
            }
        }
    }
}