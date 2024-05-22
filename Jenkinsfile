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
				sh 'docker ps; docker rm -f ${APPLICATION_NAME} || true'
				sh 'docker run -d -p 5000:5000 --name ${APPLICATION_NAME} flask-app'
			}
		}

		stage('Create Archive') {
			steps {
				sh 'zip -r "${APPLICATION_NAME}.zip" .'
			}
		}
		stage('Upload to S3') {
			steps {
				withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
					sh 'aws s3 cp ${APPLICATION_NAME}.zip s3://${S3_BUCKET_NAME}/${APPLICATION_NAME}.zip'
				}
			}
		}
	}
}