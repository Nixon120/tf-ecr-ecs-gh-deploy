pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/tbobm/tf-ecr-ecs-gh-deploy.git'
            }
        }

        stage('Build and Push to ECR') {
            steps {
                script {
                    def imageName = 'latest'
                    def ecrRepositoryUrl = '678573427865.dkr.ecr.us-east-1.amazonaws.com/app-registry'
                    def awsRegion = 'us-east-1'

                    // Build Docker image
                    sh "docker build -t ${imageName} ."

                    // Authenticate and push image to ECR
                    withAWS(region: awsRegion, credentials: 'your-aws-credentials') {
                        sh "aws ecr get-login-password --region ${awsRegion} | docker login --username AWS --password-stdin ${ecrRepositoryUrl}"
                        sh "docker tag ${imageName}:latest ${ecrRepositoryUrl}/${imageName}:latest"
                        sh "docker push ${ecrRepositoryUrl}/${imageName}:latest"
                    }
                }
            }
        }

        stage('Deploy to ECS') {
            steps {
                script {
                    def awsRegion = 'us-east-1'
                    def ecsCluster = 'ecs-cluster'
                    def ecsService = 'ecs-service'
                    def imageName = 'latest'
                    def ecrRepositoryUrl = '678573427865.dkr.ecr.us-east-1.amazonaws.com/app-registry'
                    def taskDefinition = 'your-task-definition'

                    // Update ECS task definition
                    sh "aws ecs register-task-definition --region ${awsRegion} --family ${taskDefinition} --container-definitions '[{\"name\":\"${imageName}\",\"image\":\"${ecrRepositoryUrl}/${imageName}:latest\"}]'"

                    // Update ECS service to use the new task definition
                    sh "aws ecs update-service --region ${awsRegion} --cluster ${ecsCluster} --service ${ecsService} --task-definition ${taskDefinition}"
                }
            }
        }
    }
}
