pipeline {
  agent any
  stages {
    stage('clone down') {
      steps {
        sh 'echo "hello"'
        stash(excludes: '.git', name: 'code')
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
          post {
            always {
              sh 'ls'
              deleteDir()
              sh 'ls'
            }

          }
          steps {
            unstash 'code'
            skipDefaultCheckout(true)
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
          }
        }
        stage('unit test') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          post {
            always {
              sh 'ls'
              deleteDir()
              sh 'ls'
            }

          }
          steps {
            unstash 'code'
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }

      }
    }

  }
}