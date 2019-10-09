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
        stage('Deploy') {
            steps {
                bat """java -jar target/${NAME}-${VERSION}.jar"""
            }
        }
        stage('Publish') {
        	input {
                message "Should we continue?"
                ok "Yes, we should."
                submitter "alice,bob"
                parameters {
                    string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
                    string(name: 'MACHINE', defaultValue: 'Mr Bot', description: 'Who bot should I say hello to?')
                }
            }
            steps {
                echo "Hello, ${PERSON}, nice to meet you."
            }
        }
    }
}
