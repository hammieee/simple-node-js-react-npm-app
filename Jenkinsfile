pipeline {
    agent {
        docker {
            image 'node:lts-buster-slim'
            args '-p 3000:3000'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Deliver') { 
            steps {
                sh './jenkins/scripts/deliver.sh' 
                input message: 'Finished using the web site? (Click "Proceed" to continue)' 
                sh './jenkins/scripts/kill.sh' 
            }
        }
        stage('OWASP DependencyCheck') {
		steps {
			dir('/var/jenkins_home/tools/org.jenkinsci.plugins.DependencyCheck.tools.DependencyCheckInstallation/') {
			    sh 'wget https://github.com/jeremylong/DependencyCheck/releases/download/v7.3.0/dependency-check-7.3.0-release.zip && mv dependency-check-7.3.0-release.zip Default.zip && unzip Default.zip'
			    sh 'ls -la'
			    sh 'ls -la Default'
			}
			dependencyCheck additionalArguments: '--format HTML --format XML', odcInstallation: 'Default'
		}
		post {
	            success {
			dependencyCheckPublisher pattern: 'dependency-check-report.xml'
		    }
		}
	}
    }
}

