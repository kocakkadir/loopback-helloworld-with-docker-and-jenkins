def label = "helloworld-${UUID.randomUUID().toString()}"

podTemplate(label: label,
            containers: [
                    containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true),
                    containerTemplate(name: 'nodejs', image: '255114580789.dkr.ecr.eu-west-1.amazonaws.com/nodejs', command: 'cat', ttyEnabled: true)

            ],
            volumes: [
                    hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock'),
            ])
{

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
                          docker build . -t ${SERVICE_NAME}:${GIT_COMMIT} -f Dockerfile --network host
                          docker tag ${SERVICE_NAME}:${GIT_COMMIT} ${SERVICE_NAME}:latest

                          """
                  }
              }
          }
        }
        catch (e) {
          currentBuild.result ="FAILED"
          throw e
        }
      }
    }