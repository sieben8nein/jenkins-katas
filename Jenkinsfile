pipeline {
  agent any
  environment {
    docker_username = 'sieben8nein'
  }
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
            stash(excludes: '.git', name: 'code')
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
    stage('push docker app') {
      when { branch "master"}
      environment {
        DOCKERCREDS = credentials('docker_login') //use the credentials just created in this stage
      }
      steps {
        unstash 'code' //unstash the repository code
        sh 'ci/build-docker.sh'
        sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin' //login to docker hub with the credentials above
        //input message: 'push to docker?', ok: "yes"
        sh 'ci/push-docker.sh'
      }
    }
    stage('component testing') {
      when { 
        beforeAgent true
        anyOf{
          branch 'master'; 
          changeRequest()
        }
        
      }
      steps {
        sh 'ci/component-test.sh'
      }
    }
    
  }
}