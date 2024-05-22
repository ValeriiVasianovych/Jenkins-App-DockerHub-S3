pipeline {
	agent any

	options {
		buildDiscarder(logRotator(numToKeepStr: '5'))
	}

	environment {
		AWS_CREDENTIALS  = credentials('aws-credentials')
		AWS_REGION       = 'us-east-1'
		S3_BUCKET_NAME   = 'aws-s3-flask-app-version'
		APPLICATION_NAME = 'flask-application'
	}

	stages {
		stage('Checkout') {
			steps {
				checkout scm
			}
		}
		stage('Build and Run Flask Application in Docker') {
			steps {
				sh 'docker build -t flask-app .'
				sh 'docker run -d -p 5000:5000 --name ${APPLICATION_NAME} flask-app'
			}
		}

		stage('Create Archive') {
			steps {
				sh 'sudo apt-get install zip -y'
				sh 'zip -r "${APPLICATION_NAME}.zip" .'
			}
		}
		stage('Upload to S3') {
			steps {
				withAWS(region: AWS_REGION, credentials: AWS_CREDENTIALS) {
					s3Upload(bucket: S3_BUCKET_NAME, path: "${APPLICATION_NAME}.zip")
				}
			}
		}
	}
}