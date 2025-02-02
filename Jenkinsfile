pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'amirul135/my-docer-app'
        EC2_HOST = '13.60.225.142'
        EC2_USER = 'ubuntu'
        SSH_PRIVATE_KEY = credentials('EC2_SSH_KEY')
    }

    stages {
       stage('Clone Repository') {
           steps {
               git branch: 'main', url: 'https://github.com/amirul015/ec2-deployment.git'
                 }
       }

       stage('Build Docker Image') {
           steps {
               sh 'docker build -t $DOCKER_IMAGE .'
           }
       }
      stage('Push to Docker Hub') {
         steps {
             withDockerRegistry([credentialsId: 'DOCKER_HUB_CREDENTIALS', url: '']) {
                 sh 'docker push $DOCKER_IMAGE'
             }
         }
      }

      stage('Deploy to AWS Ec2') {
         steps {
            sshagent(['EC2_SSH_KEY']) {
                sh """
                    ssh -o StrictHostKeyChecking=no $EC2_USER@$EC2_HOST << EOF
                    docker stop my-docker-app || true
                    docker rm my-docker-app || true
                    docker pull $DOCKER_IMAGE
                    docker run -d -p 5000:5000 --name my-docker-app $DOCKER_IMAGE
                    EOF
                 """
            }
         }
      }
    }
}
