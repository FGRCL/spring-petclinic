when{
	branch "master"
}
pipeline {
    agent any
    stages {
		stage ('begin'){
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
