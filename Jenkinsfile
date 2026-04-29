pipeline {
    agent any

    stages {

        stage('Build & Push') {
            steps {
                script {
                    docker.withRegistry('', 'dockerhub-creds') {
                        def app = docker.build("phumthanarat/demo-ci-cd:${BUILD_NUMBER}")
                        app.push()
                        app.push("latest")
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                podTemplate(
                    inheritFrom: 'default',
                    containers: [
                        containerTemplate(
                            name: 'kubectl',
                            image: 'bitnami/kubectl:1.29',   // ✅ แก้ตรงนี้
                            command: 'cat',
                            ttyEnabled: true
                        )
                    ]
                ) {
                    node(POD_LABEL) {
                        container('kubectl') {
                            sh """
                                kubectl version --client
                                kubectl apply -f k8s/
                                kubectl set image deployment/demo-ci-cd demo-ci-cd=phumthanarat/demo-ci-cd:${BUILD_NUMBER}
                            """
                        }
                    }
                }
            }
        }
    }
}