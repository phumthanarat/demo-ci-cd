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
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh """
                        echo $PASS | docker login -u $USER --password-stdin

                        docker build -t $IMAGE .
                        docker tag $IMAGE phumthanarat/demo-ci-cd:latest

                        docker push $IMAGE
                        docker push phumthanarat/demo-ci-cd:latest
                    """
                }
            }
        }

        stage('Deploy') {
            steps {
                podTemplate(
                    containers: [
                        containerTemplate(
                            name: 'kubectl',
                            image: 'bitnami/kubectl:1.29',
                            command: 'cat',
                            ttyEnabled: true
                        )
                    ]
                ) {
                    node(POD_LABEL) {
                        container('kubectl') {
                            sh """
                                kubectl apply -f k8s/
                                kubectl set image deployment/demo-ci-cd \
                                  demo-ci-cd=$IMAGE
                            """
                        }
                    }
                }
            }
        }
    }
}