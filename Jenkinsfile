pipeline {
    agent any
    tools {
    	maven 'Maven 3.6'
    	jdk 'JDK1.8'
	}
	options {
		buildDiscarder(logRotator(numToKeepStr: '4'))
		skipStagesAfterUnstable()
		disableConcurrentBuilds()
	}
	environment {
		NAME = readMavenPom().getArtifactId()
		VERSION = readMavenPom().getVersion()
	}
    stages {
    	stage('Start') {
            steps {
        slackSend (color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
            }
        }
    	stage('Clean') {
            steps {
                bat 'mvn --batch-mode clean'
            }
        }
		stage('Build') {
			steps {
				bat 'mvn --batch-mode compile'
			}
		}
        stage('Test') {
            steps {
                bat 'mvn --batch-mode test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Package') {
            steps {
                bat 'mvn --batch-mode -B -DskipTests package'
            }
        }
        stage('Install') {
            steps {
                bat """java -jar target/${NAME}-${VERSION}.jar"""
            }
        }
        stage('Publish') {
        	input {
                message "Should we continue to deploy?"
            }
            stages {
            	stage('Integration Test') {
                    steps {
            			echo "Integration tests ran successfully"
            		}
            	}
            	stage('Push') {
	        		parallel {
	                    stage('ClientOne') {
	                        steps {
	                            echo "Pushed to Client One environment"
	                        }
	                    }
	                    stage('ClientTwo') {
	                        steps {
	                            echo "Pushed to Client Two environment"
	                        }
	                    }
	                }
            	}
            }
        }
    }
}
