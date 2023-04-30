def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]
pipeline {
    agent any
    
    environment {
        registryCredential = 'ecr:us-east-1:awscreds'
        appRegistry = '678573427865.dkr.ecr.us-east-1.amazonaws.com/app-registry'
        awsRegistry = "https://678573427865.dkr.ecr.us-east-1.amazonaws.com"
        cluster = "ecs-cluster"
        service = "ecs-service"
    }

    stages {
        stage('Build App Image') {
            steps {
                script {
                    dockerImage = docker.build( appRegistry + ":$BUILD_NUMBER", "./Dockerfiles/App/")
                }
            }
        }
        
        stage('Upload App Image') {
          steps{
            script {
              docker.withRegistry( awsRegistry, registryCredential ) {
                dockerImage.push("$BUILD_NUMBER")
                dockerImage.push('latest')
              }
            }
          }
        }
        stage('Deploy to ECS staging') {
            steps {
                withAWS(credentials: 'awscreds', region: 'us-east-1') {
                    sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
                }
            }
        }
    }
}
