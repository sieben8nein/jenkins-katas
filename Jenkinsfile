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
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
          }
        }

      }
    }

  }
}