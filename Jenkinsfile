import jenkins.model.*
jenkins = Jenkins.instance

if(env.BRANCH_NAME == 'master'){
	pipeline {
		agent any
			stages {
				stage('Build') {
					steps {
						sh './mvnw package' 
					}
				}
			}
		}
}
