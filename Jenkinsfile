pipeline {
    agent none

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
                    sh '''
                        docker version
                        docker build -t test-image .
                    '''
                }
            }
        }
    }
}
