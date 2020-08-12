pipeline {
  agent any
  stages {
    stage('clone down') {
      steps {
        sh 'echo "hello"'
      }
    }
    stage('parallel execution') {
      parallel {
        stage('sayHello') {
          steps {
            sh 'echo "hello world"'
          }
        }

        stage('build app') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
            skipDefaultCheckout(true)
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
          }
          post {
              always {
                  sh "ls"
                  deleteDir()
                  sh "ls"
              }
          }
        }

      }
    }

  }
}