pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        slackSend(color: '#00FF00', message: "Building: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        script {
          sh 'echo "buildCount = ${buildCount}"'
          sh 'echo "lastSuccessfulCommit = ${lastSuccessfulCommit}"'
          sh 'echo "currentCommit = ${currentCommit}"'
          if(lastSuccessfulCommit == "none"){
            sh 'echo "this is the first commit"'
          } else {
            if(buildCount == 8){
              buildCount = 1
              sh 'echo "this is the eigth commit, build it"'
            } else {
              buildCount += 1
              currentBuild.result = 'SUCCESS'
              sh 'echo "this is commit ${buildCount}/8, skipping"'
            }
          }
        }

      }
    }

  }
  environment {
    buildCount = 1
    lastSuccessfulCommit = 'none'
    currentCommit = sh (script: "git log -n 1 --pretty=format:'%H'", returnStdout: true)
  }
  post {
    success {
      slackSend(color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
      script {
        lastSuccessfulCommit = sh (script: "git log -n 1 --pretty=format:'%H'", returnStdout: true)
      }

    }

    failure {
      slackSend(color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
      sh "git bisect start ${currentCommit} ${lastSuccessfulCommit}"
      sh 'git bisect run mvn clean test'
      sh 'git bisect reset'
    }

  }
}