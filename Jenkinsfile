//def buildCount = 1
//def lastSuccessfulCommit = ""
pipeline { 
	agent any 
	environment{
		//def currentCommit = sh (script: "git log -n 1 --pretty=format:'%H'", returnStdout: true)
	}
	stages {
		stage('Build') {
			steps{
				slackSend (color: '#00FF00', message: "Building: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
				script{
					if(lastSuccessfulCommit != ""){
						if(buildCount == 8){
							buildCount = 1
							sh './mvnw package'
						} else {
							buildCount += 1
							currentBuild.result = 'SUCCESS'
							error('Build ${buildCount}/8')
						}
					} else {
						sh './mvnw package'
					}
				}
			}
		}
	}
	post {
		success {
			steps{
		  		slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
				script{
					lastSuccessfulCommit = sh (script: "git log -n 1 --pretty=format:'%H'", returnStdout: true)	
				}
			}
		}

		failure {
			steps{
				slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
				sh "git bisect start ${currentCommit} ${lastSuccessfulCommit}"
				sh "git bisect run mvn clean test"
				sh "git bisect reset"
			}
		}
	}	
}