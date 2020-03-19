pipeline { 
	agent any 
	environment{
		def buildCount = 1
		def lastSuccessfulCommit = ""
		def currentCommit = sh (script: "git log -n 1 --pretty=format:'%H'", returnStdout: true)
	}
	stages {
		stage('Build') {
			steps{
				slackSend (color: '#00FF00', message: "Building: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
				sh 'echo "buildCount = ${buildCount}"'
				sh 'echo "lastSuccessfulCommit = ${lastSuccessfulCommit}"'
				sh 'echo "currentCommit = ${currentCommit}"'
				script{
					sh 'echo "buildCount = ${buildCount}"'
					sh 'echo "lastSuccessfulCommit = ${lastSuccessfulCommit}"'
					sh 'echo "currentCommit = ${currentCommit}"'
					if(env.lastSuccessfulCommit != ""){
						if(buildCount == 8){
							buildCount = 1
							sh 'echo "this is a successful build process"'
						} else {
							buildCount += 1
							currentBuild.result = 'SUCCESS'
							error('Build ${buildCount}/8')
						}
					} else {
						sh 'echo "this is a successful build process"'
					}
				}
			}
		}
	}
	post {
		success {
		  	slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
			script{
				lastSuccessfulCommit = sh (script: "git log -n 1 --pretty=format:'%H'", returnStdout: true)	
			}
		}

		failure {
			slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
			sh "git bisect start ${currentCommit} ${lastSuccessfulCommit}"
			sh "git bisect run mvn clean test"
			sh "git bisect reset"
		}
	}	
}