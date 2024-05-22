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

        stage('Login to Docker Hub') {
            steps {
                withCredentials([[
                    $class: 'UsernamePasswordMultiBinding',
                    credentialsId: "${DOCKERHUB_CREDENTIALS}",
                    usernameVariable: 'DOCKERHUB_USERNAME',
                    passwordVariable: 'DOCKERHUB_PASSWORD'
                ]]) {
                    sh "docker login -u ${DOCKERHUB_USERNAME} -p ${DOCKERHUB_PASSWORD}"
                }
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
                sh "zip -r ${env.ARCHIVE_NAME} ."
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
                    sh "aws s3 cp ${env.ARCHIVE_NAME} s3://${S3_BUCKET_NAME}/${env.ARCHIVE_NAME}"
                }
            }
        }
    }

    post {
        always {
            cleanWs()
            sh 'docker logout'
        }
    }
}
