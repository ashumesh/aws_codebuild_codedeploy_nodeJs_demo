pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="274213768634"
        AWS_DEFAULT_REGION="us-east-1" 
        IMAGE_REPO_NAME="dockerrepo"
        IMAGE_TAG="latest"
        REPOSITORY_URI = "${274213768634.dkr.ecr.us-east-1.amazonaws.com/dockerrepo}"
    }
   
   
    stages {
        
         stage('Logging into AWS ECR') {
            steps {
                script {
                sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
                }
                 
            }
        }
        
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ashumesh/aws_codebuild_codedeploy_nodeJs_demo']]])     
            }
        }
  
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
                sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
                sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                sh "docker rmi jenkin-pipeline-build-demo"
                sh "docker stop nodeapp && docker rm nodeapp && docker rmi 274213768634.dkr.ecr.us-east-1.amazonaws.com/dockerrepo"
                sh "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 274213768634.dkr.ecr.us-east-1.amazonaws.com"
                sh "docker run --name nodeapp -itd -p 3000:3000 274213768634.dkr.ecr.us-east-1.amazonaws.com/dockerrepo"
                sh 'pwd'
                sh "chmod +x remove.sh"
                sh "./remove.sh"
                }
           }
       }
    }
}
