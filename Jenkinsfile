pipeline {
  agent any
  stages {
    stage('Top-Clone down') {
      stage('Mid-Clone down') {
        steps {
            sh 'echo "clone down step!"'
        }
      }
    }
    
    stage('Parallel Execution') {
      parallel {

        stage('Say Hello') {
          steps {
            sh 'echo "Hello world!"'
          }
        }

        stage('Build app') {
          agent {
            docker {
              image 'gradle:jdk11'
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