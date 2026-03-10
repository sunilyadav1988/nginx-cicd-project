pipeline {
    agent any

    environment {
        IMAGE_NAME = "sunil093/nginx-cicd"
        EC2_HOST = "3.111.47.54"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git 'https://github.com/sunilyadav1988/nginx-cicd-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub',
                usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'docker login -u $USER -p $PASS'
                    sh 'docker push $IMAGE_NAME:latest'
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@$EC2_HOST '
                    docker pull $IMAGE_NAME:latest
                    docker stop nginx || true
                    docker rm nginx || true
                    docker run -d -p 80:80 --name nginx $IMAGE_NAME:latest
                    '
                    '''
                }
            }
        }
    }
}
