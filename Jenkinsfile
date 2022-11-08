pipeline {
    
    environment {
     //environment settings for ECR & ECS deploy script//
     AWS_ACCOUNT_ID="447151167969"
     AWS_DEFAULT_REGION="eu-west-2" 
     CLUSTER_NAME="academy-ecs-cl"
     SERVICE_NAME="ec-acad-ecs-srv01"
     TASK_DEFINITION_NAME="ec-acad-task-def01"
     DESIRED_COUNT="1"
     IMAGE_REPO_NAME="ec-acad-01-java-app"
     IMAGE_TAG="${env.BUILD_ID}"
     REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
     registryCredentialDev = 'ec-acad-dev-credentials'
     registryCredentialTest = 'ec-acad-test-credentials'
     dockerImage = ''
    }
    
    agent any

    tools {maven "maven-3.8.6"}	
	
    stages {

    // Maven build and Unit Tests
    stage('Build & Unit Tests') {
      steps{
        script {
          sh 'mvn -B clean package'
        }
      }
    }
        // Building Docker image
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "ec-acad-01-java-app:${env.BUILD_ID}"
        }
      }
    }
	//Push image to AWS ECR   
       stage('Push image to AWS ECR') {
        steps{
            script{
                docker.withRegistry("https://" + REPOSITORY_URI, "ecr:eu-west-2:" + registryCredentialDev) {
                    dockerImage.push()
                }
            }
        }
       }
    // Cleanup
    stage('Image Cleanup') {
      steps{
        script {
	 catchError(buildResult: 'SUCCESS', stageResult: 'SUCCESS') {
	    sh 'echo "cleaning images"'
	    sh 'docker rmi -f $(docker images -a -q)' /* clean up dockerfile images*/
            sh "exit 0"
                }
        }
      }
    }
	   
         //Deploying Image to Dev ECS
     stage('Deploy to Dev') {
     steps{
            withAWS(credentials: registryCredentialDev, region: "${AWS_DEFAULT_REGION}") {
                script {
			sh "chmod +x ./scripts/dev-script.sh"
			sh './scripts/dev-script.sh'
                }
            } 
        }
      } 	   

               //Deploying Image to Test ECS
     stage('Deploy to Test') {
     steps{
            withAWS(credentials: registryCredentialTest, region: "${AWS_DEFAULT_REGION}") {
                script {
			input id: 'Choice', message: 'Deploy image to Test?'
			sh "chmod +x ./scripts/test-script.sh"
			sh './scripts/test-script.sh'
                }
            } 
        }
      } 
	    
}
}
