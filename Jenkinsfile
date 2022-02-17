pipeline {
    agent any
    environment {
        registry = '736860399682.dkr.ecr.ap-south-1.amazonaws.com/my-docker-image'
    }
    stages {
        stage('SCM Checkout') {
            steps {
                git 'https://github.com/gitniran/myPythonDockerRepo.git'
            }
        }
        // Building Docker images
        stage('Building image') {
            steps {
                script {
                    dockerimage = docker.build registry
                }
            }
        }
        // Uploading Docker images into AWS ECR
        stage('Pushing to ECR') {
            steps {  
                script {
                sh 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 736860399682.dkr.ecr.ap-south-1.amazonaws.com'
                sh 'docker push 736860399682.dkr.ecr.ap-south-1.amazonaws.com/my-docker-image:latest'

                }
            }
        }
        // Stopping Docker containers for cleaner Docker run
        stage('stop previous containers') {
            steps {
                sh 'docker ps -f name=mypythonContainer -q | xargs --no-run-if-empty docker container stop'
                sh 'docker container ls -a -fname=mypythonContainer -q | xargs -r docker container rm'
            }
       }
       stage('Docker Run') {
            steps{
                script {
                sh 'docker run -d --rm --name mypythonContainer --log-driver=awslogs --log-opt awslogs-region=ap-south-1 --log-opt awslogs-group="dockerlogs" --log-opt awslogs-create-group=true -p 8096:5000 736860399682.dkr.ecr.ap-south-1.amazonaws.com/my-docker-image:latest'
                }
            }
        }
    }
}
