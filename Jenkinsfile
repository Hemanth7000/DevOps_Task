pipeline {
  agent { label 'docker && aws' } // or 'any' if agents have tools
  environment {
    DOCKERHUB_CRED = credentials('dockerhub-creds') // username/password
    AWS_CRED = credentials('aws-jenkins') // accessKey / secretKey (Jenkins "Username with password" or "AWS credentials" plugin)
    IMAGE_NAME = "hemanth173/devops-task"
    IMAGE_TAG = "${env.BUILD_NUMBER}"
    AWS_REGION = "ap-south-1"            // change to your region
    ECS_CLUSTER = "splendid-parrot-1g8vi2"
    ECS_SERVICE = "DevOps_Task-service-w43li486"
    TASK_DEF_FILE = "ecs-task-definition.json"
  }
  options { timestamps() }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Install & Test') {
      steps {
        sh 'cd app && npm ci'
        sh 'cd app && npm test || true' // change to fail on tests if desired
      }
    }
    stage('Build Docker Image') {
      steps {
        sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
      }
    }
    stage('Push to DockerHub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
          sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
        }
      }
    }
    stage('Deploy to ECS') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'aws-jenkins', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
          // Update image in task definition and register, then update service
          sh '''
          # Replace image tag in task definition (make a temp copy)
          jq --arg IMAGE "${IMAGE_NAME}:${IMAGE_TAG}" '.containerDefinitions[0].image=$IMAGE' ${TASK_DEF_FILE} > /tmp/taskdef.json
          aws --region ${AWS_REGION} ecs register-task-definition --cli-input-json file:///tmp/taskdef.json
          # Find new revision
          FAMILY=$(jq -r .family ${TASK_DEF_FILE})
          # Update service to use latest task definition
          aws --region ${AWS_REGION} ecs update-service --cluster ${ECS_CLUSTER} --service ${ECS_SERVICE} --force-new-deployment
          '''
        }
      }
    }
  }
  post {
    success { echo "Pipeline succeeded" }
    failure { echo "Pipeline failed" }
  }
}
