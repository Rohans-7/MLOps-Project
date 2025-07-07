pipeline {
    agent any

    environment {
        IMAGE_NAME = 'imrds7/mlops-app'
        DOCKERHUB_CREDENTIALS = 'DockerHubCred'
        GIT_REPO_URL = 'https://github.com/Rohans-7/MLOps-Project.git'
        BRANCH = 'main'

        MONGO_URL = credentials('mongo-url-id')
        AWS_ACCESS_KEY_ID = credentials('aws-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-id')
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: "${BRANCH}", url: "${GIT_REPO_URL}"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE_NAME}")
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKERHUB_CREDENTIALS}") {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                script {
                    withEnv(["ANSIBLE_HOST_KEY_CHECKING=False"]) {
                        sh """
                            ansible-playbook deploy.yml -i inventory \
                            -e IMAGE_NAME=${IMAGE_NAME} \
                            -e MONGO_URL=${MONGO_URL} \
                            -e AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID} \
                            -e AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
                        """
                    }
                }
            }
        }
    }

    post {
        failure {
            echo "Pipeline failed!"
        }
        success {
            echo "Deployment completed successfully!"
        }
    }
}
