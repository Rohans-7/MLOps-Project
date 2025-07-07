pipeline {
    agent any

    environment {
        IMAGE_NAME = 'imrds7/mlops-app'
        DOCKERHUB_CREDENTIALS = 'DockerHubCred'
        GIT_REPO_URL = 'https://github.com/Rohans-7/MLOps-Project.git'
        BRANCH = 'main'
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
                    ansiblePlaybook(
                        playbook: 'deploy.yml',
                        inventory: 'inventory'
                    )
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
