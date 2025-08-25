pipeline {
    agent any 

    stages {
        stage('Build docker image') {
            steps {  
                echo "Building Docker image..."
                sh '''
                    docker build -t karunya1203/flaskapp:${BUILD_NUMBER} .
                '''
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'test1', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                    echo "Logging in to Docker Hub..."
                    sh '''
                        echo "${DOCKERHUB_PASSWORD}" | docker login -u "${DOCKERHUB_USERNAME}" --password-stdin
                    '''
                }
            }
        }

        stage('Push image') {
            steps {
                echo "Pushing Docker image to Docker Hub..."
                sh '''
                    docker push karunya1203/flaskapp:${BUILD_NUMBER}
                '''
            }
        }

        stage('Deploy to Staging') {
            steps {
                echo "Deploying container..."
                sh '''
                    docker pull karunya1203/flaskapp:${BUILD_NUMBER}
                    docker stop myapp-container || true
                    docker rm myapp-container || true
                    docker run -d --name myapp-container -p 8000:8000 karunya1203/flaskapp:${BUILD_NUMBER}
                '''
            }
        }
    }

    post {
        always {
            echo "Logging out from Docker Hub..."
            sh 'docker logout'
        }
    }
}
