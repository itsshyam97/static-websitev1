pipeline {

    agent any

    environment {
        IMAGE_NAME = "shyamprakashs/personal-website"
        IMAGE_TAG  = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Image') {
            steps {
                sh '''
                docker build -t $IMAGE_NAME:$IMAGE_TAG .
                docker tag $IMAGE_NAME:$IMAGE_TAG $IMAGE_NAME:latest
                '''
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {

                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                docker push $IMAGE_NAME:$IMAGE_TAG
                docker push $IMAGE_NAME:latest
                '''
            }
        }

        stage('Deploy') {
            steps {

                sh '''
                docker stop personal-website || true
                docker rm personal-website || true

                docker run -d \
                    --name personal-website \
                    -p 8090:80 \
                    $IMAGE_NAME:latest
                '''
            }
        }

        stage('Health Check') {

            steps {

                sh '''

                sleep 5

                docker exec personal-website \
                curl -I http://localhost

                '''
            }

        }

    }

    post {

        success {
            echo "Deployment Successful!"
        }

        failure {
            echo "Deployment Failed!"
        }

        always {
            echo "Pipeline Finished"
        }

    }

}
