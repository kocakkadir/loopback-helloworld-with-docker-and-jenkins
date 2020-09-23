def label = "helloworld-${UUID.randomUUID().toString()}"

    node(label) {
        properties([disableConcurrentBuilds()])
        try {

          stage('Checkout') {
              checkout scm
          }
          stage('UnitTest') {
            container('nodejs') {
                sh """
                npm install
                npm test
                """
            }
          }
          stage('Build docker image') {
              if(env.BRANCH_NAME == "master") {
                  def GIT_COMMIT = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                  def envMap = [
                     master: 'master'
                  ]
                  def env_x = envMap[env.BRANCH_NAME]
                  container('docker') {
                      def SERVICE_NAME = "helloworld-${env_x}"

                          sh """
                          docker build . -t ${SERVICE_NAME}:${GIT_COMMIT} -f Dockerfile-k8s --network host
                          docker tag ${SERVICE_NAME}:${GIT_COMMIT} ${DOCKER_IMAGE_REPO}:${GIT_COMMIT}
                          docker tag ${SERVICE_NAME}:${GIT_COMMIT} ${DOCKER_IMAGE_REPO}:latest

                          """
                  }
              }
          }