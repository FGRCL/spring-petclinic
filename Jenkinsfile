def buildCount
def lastSuccessfulCommit
def currentCommit
pipeline { 
	agent any 

	stages {
		stage('Setup variables'){
			steps{
				script{
					currentCommit = "env.GIT_COMMIT"
					def buildCountExists = fileExists 'buildCount'
					if(buildCountExists){
						buildCount = readFile('buildCount')
					}else{
						buildCount = 1;
					}

					def lastSuccessfulCommitExists = fileExists 'lastSuccessfulCommit'
					if(lastSuccessfulCommitExists){
						lastSuccessfulCommit = readFile('lastSuccessfulCommit')
					}else{
						lastSuccessfulCommit = currentCommit
					}
				}
			}
		}
		stage('Build') {
			steps{
				slackSend (color: '#00FF00', message: "Building: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
				script{
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
		stage('Update build count'){
			steps{
				script{
					sh 'echo "new buildCount = ${buildCount}"'
					writeFile('buildCount', buildCount)
				}
			}
		}
	}

  post {
    success {
      slackSend(color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
      script {
        lastSuccessfulCommit = currentCommit
		writeFile('lastSuccessfulCommit', lastSuccessfulCommit)
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