pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ECR_REPOSITORY = 'app-registry'
        ECS_CLUSTER = 'ecs-cluster'
        ECS_SERVICE = 'ecs-service'
        DOCKER_IMAGE_TAG = 'latest' // Modify this as per your requirements
    }

    stages {
        stage('Build') {
            steps {
                script {
                    // Build your application here
                    sh 'docker build -t $ECR_REPOSITORY:$DOCKER_IMAGE_TAG .'
                }
            }
        }

        stage('Publish to ECR') {
            steps {
                script {
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'your_aws_credentials_id']]) {
                        sh """
                            $(aws ecr get-login --no-include-email --region $AWS_REGION)
                            docker push $ECR_REPOSITORY:$DOCKER_IMAGE_TAG
                        """
                    }
                }
            }
        }

        stage('Deploy to ECS') {
            steps {
                script {
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'your_aws_credentials_id']]) {
                        sh """
                            $(aws ecr get-login --no-include-email --region $AWS_REGION)
                            aws ecs update-service --cluster $ECS_CLUSTER --service $ECS_SERVICE --force-new-deployment
                        """
                    }
                }
            }
        }
    }
}



