pipeline {
    agent any

    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }

    environment {
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
                script {
                    env.ARCHIVE_NAME = "${APPLICATION_NAME}_\$(date +%Y%m%d%H%M%S).zip"
                }
                sh 'zip -r "${ARCHIVE_NAME}" .'
            }
        }
        stage('Upload to S3') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                    sh 'aws s3 cp ${ARCHIVE_NAME} s3://${S3_BUCKET_NAME}/${ARCHIVE_NAME}'
                }
            }
        }
    }
}
