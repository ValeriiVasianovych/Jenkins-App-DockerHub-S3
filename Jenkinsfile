pipeline {
    agent any

    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }

    environment {
        S3_BUCKET_NAME        = 'aws-s3-flask-app-version'
        APPLICATION_NAME      = 'flask-application'
        DOCKERHUB_USERNAME    = 'valeriivasianovych'
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
    }

    stages {
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKERHUB_USERNAME}/${APPLICATION_NAME}:latest ."
            }
        }

        steps('login') {
            steps {
                sh 'docker login -u ${DOCKERHUB_USERNAME} -p ${DOCKERHUB_CREDENTIALS}'
            }
        }

        stage('Push Docker Image') {
            steps {
                sh "docker push ${DOCKERHUB_USERNAME}/${APPLICATION_NAME}:latest"
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
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-credentials',
                    accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                    secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                ]]) {
                    sh 'aws s3 cp "${ARCHIVE_NAME}" s3://${S3_BUCKET_NAME}/${ARCHIVE_NAME}'
                }
            }
        }
        stage('Post-build Cleanup and Logout') {
            always {
                cleanWs()
                sh 'docker logout'
            }
        }
    }
}
