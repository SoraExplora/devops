pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials') 
        DOCKER_IMAGE = "yahya123/myapp"
        TAG = "1.0.${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/….git'
            }
        }

        stage('Build App') {
            steps {
                sh 'npm install'      // or mvn package / whatever your app uses
            }
        }

        stage('Docker Build') {
            steps {
                sh """
                docker build -t ${DOCKER_IMAGE}:${TAG} .
                """
            }
        }

        stage('Docker Login') {
            steps {
                sh """
                echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                """
            }
        }

        stage('Docker Push') {
            steps {
                sh """
                docker push ${DOCKER_IMAGE}:${TAG}
                """
            }
        }

        stage('Deploy (Optional)') {
            steps {
                sh """
                docker pull ${DOCKER_IMAGE}:${TAG}
                docker stop myapp || true
                docker rm myapp || true
                docker run -d --name myapp -p 8080:8080 ${DOCKER_IMAGE}:${TAG}
                """
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}
