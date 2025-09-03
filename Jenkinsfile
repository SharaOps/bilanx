pipeline {
    agent any

    parameters {
        string(name: 'DOCKER_TAG', defaultValue: 'latest', description: 'Docker image tag (e.g. v1.0.0, build-123, latest)')
    }

    environment {
        DOCKER_REGISTRY = "registry.digitalocean.com"   
        DOCKER_REPO     = "horillaappregistry/horilla" 
        IMAGE_TAG       = "${params.DOCKER_TAG}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://github.com/SharaOps/bilanx.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_REPO}:${IMAGE_TAG}")
                }
            }
        }

        stage('Login to Registry') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: '7ff9d576-4620-4756-962b-d058842943d4', 
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh "echo $DOCKER_PASS | docker login ${DOCKER_REGISTRY} -u $DOCKER_USER --password-stdin"
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    dockerImage.push()
                }
            }
        }
    }

    post {
        always {
            sh "docker logout ${DOCKER_REGISTRY}"
        }
    }
}
