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
            	echo ${NAME}
            	echo $NAME
       			pom = readMavenPom file: 'pom.xml'
				echo $pom
                bat 'java -jar target/${NAME}-${VERSION}.jar'
            }
        }
    }
}
