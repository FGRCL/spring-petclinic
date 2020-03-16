pipeline {
    agent any
	if(env.BRANCH_NAME == 'master'){
		stages {
			stage('Build') {
				steps {
					sh './mvnw package' 
				}
			}
		}
	}
}
