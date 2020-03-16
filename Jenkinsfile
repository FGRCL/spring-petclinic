pipeline {
    agent any
    node {
		if(env.BRANCH_NAME == 'master'){
			stage('Build') {
				steps {
					sh './mvnw package' 
				}
			}
    	}
	}
}
