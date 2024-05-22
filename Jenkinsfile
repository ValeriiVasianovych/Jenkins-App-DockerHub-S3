pipeline {
    agent any

    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }

    environment {
        S3_BUCKET_NAME      = 'aws-s3-flask-app-version'
        APPLICATION_NAME    = 'flask-application'
		EB_ENVIRONMENT_NAME = 'flask-app-env'
		REGION              = 'us-east-1'
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
                script {
                    def formattedDateTime = sh(script: 'date +%Y%m%d%H%M%S', returnStdout: true).trim()
                    env.ARCHIVE_NAME = "${APPLICATION_NAME}_${formattedDateTime}.zip"
                }
                sh 'zip -r "${ARCHIVE_NAME}" .'
            }
        }
        stage('Upload to S3') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
					sh 'aws s3 cp "${ARCHIVE_NAME}" s3://${S3_BUCKET_NAME}/${ARCHIVE_NAME}'
				}
            }
        }
		stage('Deploy to Elastic Beanstalk') {
			steps {
				env.BUILD_NUMBER = sh(script: 'date +%Y%m%d%H%M%S', returnStdout: true).trim()
				withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
					sh "aws elasticbeanstalk create-application-version --application-name ${APPLICATION_NAME} --version-label ${BUILD_NUMBER} --source-bundle S3Bucket=${S3_BUCKET_NAME},S3Key=${ARCHIVE_NAME}"
					sh "aws elasticbeanstalk update-environment --application-name ${APPLICATION_NAME} --environment-name ${EB_ENVIRONMENT_NAME} --version-label ${BUILD_NUMBER}"
				}
			}
		}
    }
}
