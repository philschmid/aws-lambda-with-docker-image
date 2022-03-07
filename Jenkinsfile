pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="884202029588"
        AWS_DEFAULT_REGION="us-east-1" 
        IMAGE_REPO_NAME="docker-lambda"
        IMAGE_TAG="latest"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
        dockerImage = ''
    }
   
    stages {
 
        stage('Install sam-cli') {
          if (fileExists('aws-sam-cli-linux-x86_64.zip')) {
          steps {            
              sh '''
               rm aws-sam-cli-linux-x86_64.zip
               rm -rf sam-installation
               wget https://github.com/aws/aws-sam-cli/releases/latest/download/aws-sam-cli-linux-x86_64.zip
               unzip aws-sam-cli-linux-x86_64.zip -d sam-installation
               ./sam-installation/install --update
               /usr/local/bin/sam --version
            '''
            }
          }
          }
        
         
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/mmsmannam/aws-lambda-with-docker-image.git']]])     
            }
        }
  
  
       stage('Logging into AWS ECR') {
            steps {
                script {
                sh "\$(aws ecr get-login --no-include-email --region us-east-1)"
                sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 884202029588.dkr.ecr.us-east-1.amazonaws.com"
                }
                
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
         }
        }
      }
      
     
      
      stage('Sam deployment') {
          steps{  
         script {
            sh "/usr/local/bin/sam deploy --image-repository '${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG} --no-confirm-changeset --no-fail-on-empty-changeset --capabilities CAPABILITY_IAM CAPABILITY_AUTO_EXPAND'"   
          }
     }
     }
} 
}