pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Image') {
            steps {
                sh 'docker build -t demo-app:${BUILD_NUMBER} .'
            }
        }

        stage('Load Image to Kind') {
            steps {
                sh 'kind load docker-image demo-app:${BUILD_NUMBER} --name dev-cluster'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl set image deployment/demo-app \
                demo-container=demo-app:${BUILD_NUMBER} --record
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'kubectl rollout status deployment/demo-app'
            }
        }

    }

    post {
        failure {
            echo "Deployment failed. Rolling back..."
            sh '''
            if kubectl get deployment demo-app > /dev/null 2>&1; then
                kubectl rollout undo deployment/demo-app
            else
                echo "Deployment not found, skipping rollback"
            fi
            '''
        }
    }
}