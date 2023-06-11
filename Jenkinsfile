pipeline {
     agent {
        node {
        
       label 'slave'
      
           }
    }
    environment {
        AWS_ACCOUNT_ID="306272608230"
        AWS_DEFAULT_REGION="us-east-2" 
	CLUSTER_NAME="First-CICD"
	SERVICE_NAME="nodejs-container-service"
	TASK_DEFINITION_NAME="first-run-task-defination"
	DESIRED_COUNT="1"
        IMAGE_REPO_NAME="rahul"
        IMAGE_TAG="${env.BUILD_ID}"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
	registryCredential = "master"
    }
   
    stages {

    // Tests
    stage('Unit Tests') {
      steps{
        script {
          sh 'npm install'
	  sh 'npm test -- --watchAll=false'
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
	    
	     //stage('Pushing to ECR') {
     //steps{  
       //  script {
	//		docker.withRegistry("https://" + REPOSITORY_URI, "ecr:${AWS_DEFAULT_REGION}:" + registryCredential) {
         //           	dockerImage.push()
           //     	}
         //}
        //}
      //}
	    
    stage('Pushing to ECR') {
     steps{  
         sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
	 sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:${IMAGE_TAG}"
	 sh "docker push ${REPOSITORY_URI}:${IMAGE_TAG}"    
	 sh "docker rmi -f ${REPOSITORY_URI}:${IMAGE_TAG}"
	 //sh "docker push ${REPOSITORY_URI}:${commit_id}"
        // Clean up
        //sh "docker rmi -f ${REPOSITORY_URI}:${commit_id}"
        }
      }
      
    stage('Deploy') {
     steps{
            withAWS(credentials: registryCredential, region: "${AWS_DEFAULT_REGION}") {
                script {
			sh './script.sh'
                }
            } 
        }
      }      
      
    }
}

