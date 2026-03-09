pipeline {
 agent any

 stages {

  stage('Build Image') {
   steps {
    sh 'docker build -t demo-app:$BUILD_NUMBER .'
   }
  }

  stage('Load Image to Kind') {
   steps {
    sh 'kind load docker-image demo-app:$BUILD_NUMBER'
   }
  }

  stage('Deploy to Kubernetes') {
   steps {
    sh '''
    kubectl set image deployment/demo-app \
    demo-container=demo-app:$BUILD_NUMBER
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
   sh 'kubectl rollout undo deployment/demo-app'
  }
 }
}