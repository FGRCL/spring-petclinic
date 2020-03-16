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
				script{
					if(lastSuccessfulCommit != ""){
						if(buildCount == 8){
							buildCount = 1
							sh 'mvn clean install'
						} else {
							buildCount += 1
							step {
								error('Build ${buildCount}/8')
							}
							currentBuild.result = 'SUCCESS'
							return
						}
					} else {
						sh 'mvn clean install'
					}
				}
			}
		}
		stage('Test'){
			steps { 
				slackSend (color: '#00FF00', message: "Testing: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
				sh 'mvn test'
			} 
		}
		stage('package'){
			steps { 
				slackSend (color: '#00FF00', message: "Packaging: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
				sh './mvnw package'
				
			}
		}
		stage('deploy'){
			steps { 
				slackSend (color: '#00FF00', message: "Deploying: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
				sh './mvnw deploy -DaltDeploymentRepository=internal.repo::default::"file:///mnt/c/Users/Fgrcl/My Cloud/Semester 6/SOEN  345/ASSIGNMENTS/a6/Jenkins/Deploy"'
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
		}
	}	
}