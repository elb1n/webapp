pipeline {
  agent any

  stages {

    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/elb1n/webapp.git'
      }
    }

    stage('Docker Image Build') {
      steps {
        sh 'docker build -t webapp:latest .'
      }
    }

    stage('Container Run Test') {
      steps {
        sh '''
          docker stop webapp-ci 2>/dev/null || true
          docker rm webapp-ci 2>/dev/null || true
          docker run -d --name webapp-ci webapp:latest
          sleep 5
          docker exec webapp-ci wget -qO- http://localhost:3000/health
          echo "Health check passed!"
          docker stop webapp-ci
          docker rm webapp-ci
        '''
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh 'kubectl apply -f k8s/deployment.yaml'
        sh 'kubectl rollout status deployment/webapp'
      }
    }

  }

  post {
    success { echo 'Pipeline succeeded! webapp deployed.' }
    failure { echo 'Pipeline failed. Check logs.' }
  }
}
