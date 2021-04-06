pipeline {
  agent any
  environment { 
        docker_username = 'villesalomakiefi' //insert your own docker username here
  }
  stages {
    stage('clone down') {
      steps {
        stash(excludes: '.git', name: 'code')
        deleteDir()
      }
    }

    stage('Test and build') {
      parallel {
        stage('test app') {
          options {
            skipDefaultCheckout(true)
          }
          agent {
            docker {
              image 'gradle:jdk11'
            }
          }
          steps {
            unstash 'code'
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }
        stage('build app') {
          options {
            skipDefaultCheckout(true)
          }
          agent {
            docker {
              image 'gradle:jdk11'
            }
          }
          steps {
            unstash 'code'
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            stash(excludes: '.git', name: 'code')
          }
        }
      }
    }
    stage('build and push docker') {
      when { branch "master" }
      options {
        skipDefaultCheckout(true)
      }
      environment {
        DOCKERCREDS = credentials('docker_login')
      }
      steps {
        unstash 'code'
        sh 'ci/build-docker.sh'
        //pushIfMaster() // This is a script inside a declarative pipeline
      }
    }
    stage('component test') {
      options {
        skipDefaultCheckout(true)
      }
      steps {
        unstash 'code'
        sh 'ci/component-test.sh'
      }
    }

  } 
}
void pushIfMaster() {
    if (BRANCH_NAME=="master"){
      sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin'
      sh 'ci/push-docker.sh'
    }

}
