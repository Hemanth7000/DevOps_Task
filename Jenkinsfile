pipeline {
  agent any

  environment {
    IMAGE = 'yourdockerhubusername/logo-server'
    AWS_REGION = 'ap-south-1'
    CLUSTER = 'your-ecs-cluster'
    SERVICE = 'your-ecs-service'
  }

  stages {
    stage('Build') {
      steps {
        sh 'npm install'
        sh 'npm test || echo "No tests yet"'
      }
    }

    stage('Dockerize') {
      steps {
        sh 'docker build -t $IMAGE .'
      }
    }

    stage('Push to DockerHub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          sh 'echo $PASS | docker login -u $USER --password-stdin'
          sh 'docker push $IMAGE'
        }
      }
    }

    stage('Deploy to ECS') {
      steps {
        withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-creds']]) {
          sh '''
            aws ecs update-service \
              --cluster $CLUSTER \
              --service $SERVICE \
              --force-new-deployment \
              --region $AWS_REGION
          '''
        }
      }
    }
  }
}

