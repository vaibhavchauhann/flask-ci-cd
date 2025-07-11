pipeline {
  agent any

  environment {
    REPO_URL = 'https://github.com/your-username/flask-ci-cd.git'
    DOCKER_IMAGE = 'yourdockerhub/flask-app'
    DEPLOY_SERVER = 'ec2-user@<your-ec2-ip>'
  }

  stages {
    stage('Clone') {
      steps {
        git REPO_URL
      }
    }

    stage('Build Image') {
      steps {
        sh 'docker build -t ${DOCKER_IMAGE}:latest .'
      }
    }

    stage('Push Image') {
      steps {
        sh '''
          echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
          docker push ${DOCKER_IMAGE}:latest
        '''
      }
    }

    stage('Deploy') {
      steps {
        sshagent(['deploy-key']) {
          sh '''
            ssh ${DEPLOY_SERVER} "
              docker pull ${DOCKER_IMAGE}:latest &&
              docker stop flask-container || true &&
              docker rm flask-container || true &&
              docker run -d --name flask-container -p 5000:5000 ${DOCKER_IMAGE}:latest
            "
          '''
        }
      }
    }
  }

  post {
    always {
      echo 'Pipeline execution completed.'
    }
  }
}
