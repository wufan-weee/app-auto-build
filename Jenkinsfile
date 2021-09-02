pipeline {
  agent any
  environment {
      IONIC_TOKEN = credentials('ionic-token')
  }
  stages {
    stage('NPM Tests') {
      agent {
        docker {
          image  'ghcr.io/ionic-team/ionic-cli:6.11.11'
          args '-v $PWD:/usr/src/app/ -u 0:0'
        }
      }
      steps {
          sh 'npm ci'
          sh 'npm run test'
      }
    }

    stage('Build') {
      parallel {
        stage('Build Android') {
          agent {
            docker {
              image  'ghcr.io/ionic-team/ionic-cli:6.11.11'
              args '-v $PWD:/usr/src/app/ -u 0:0'
            }
          }
          steps {
              sh 'ionic package build android debug --environment="Jenkins" --native-config"=Jenkins" --build-file-name="Jenkins-$BUILD_ID.apk"'
          }
          post {
            success {
              archiveArtifacts artifacts: 'Jenkins-*.apk', fingerprint: true
            }
          }
        }
      }
    }
  }
}