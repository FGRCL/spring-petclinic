pipeline {
    agent any
    stages {
		stage ('begin'){
			when{
				branch "master"
			}	
			steps{

				sh 'echo start'
			}
		}
		stage('Build') {
			steps {
				sh './mvnw package' 
			}
		}
	}
}
