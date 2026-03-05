/*=========================================================
# ecommerece
  =========================================================*/

pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    environment {
        SERVICE_NAME = "ecommerce"
        DOCKERHUB_USER = "lakshvar96"
        IMAGE_NAME = "${DOCKERHUB_USER}/${SERVICE_NAME}"
    }

    stages {

        stage('Checkout') {
            
            steps {
                cleanWs()
                checkout scm
                stash name: 'source-code', includes: '**/*'
            }
        }

        /* ===================== Build Maven Stage ===================== */
        stage('Build') {
           
            tools {
                maven 'maven'
            }

            steps {
                unstash 'source-code'
                sh 'mvn clean install -DskipTests'
            }
        }

        stage('Docker Build') {
            
            steps {
                cleanWs()
                unstash 'source-code'
                
                    sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
                
            }
        }

        

        stage('Push to DockerHub') {
            
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push ${IMAGE_NAME}:${BUILD_NUMBER}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ ${SERVICE_NAME} Pipeline SUCCESS"
        }
        failure {
            echo "❌ ${SERVICE_NAME} Pipeline FAILED"
        }
    }
}


