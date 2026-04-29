stage('Build & Push') {
  container('kaniko') {
    sh '''
      export DOCKER_CONFIG=/kaniko/.docker

      /kaniko/executor \
        --context . \
        --dockerfile Dockerfile \
        --destination phumthanarat/demo-ci-cd:${BUILD_NUMBER} \
        --destination phumthanarat/demo-ci-cd:latest
    '''
  }
}
