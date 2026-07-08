pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t personal-website .'
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                docker stop personal-website || true
                docker rm personal-website || true

                docker run -d \
                  --name personal-website \
                  -p 8090:80 \
                  personal-website
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'curl http://localhost:8090'
            }
        }
    }

    post {

        success {
            echo 'Deployment Successful!'
        }

        failure {
            echo 'Deployment Failed!'
        }

        always {
            echo 'Pipeline Finished'
        }
    }
}
