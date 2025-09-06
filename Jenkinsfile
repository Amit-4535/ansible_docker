pipeline {
    agent any

    environment {
        DOCKER_IMAGE   = "amit4535/nginx-custom"   // your DockerHub repo name
        DOCKER_TAG     = "v1"                     // version tag
        CONTAINER_NAME = "nginx_container"        // container you already created
        REGISTRY_CRED  = "dockerhub-cred"         // Jenkins credentials ID for DockerHub
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/Amit-4535/ansible_docker.git'
            }
        }

        stage('Commit Container as Image') {
            steps {
                sh '''
                    echo "📦 Committing running container ${CONTAINER_NAME} as new image..."
                    docker commit ${CONTAINER_NAME} ${DOCKER_IMAGE}:${DOCKER_TAG}
                '''
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${REGISTRY_CRED}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "🔑 Logging into DockerHub..."
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                    echo "🚀 Pushing image ${DOCKER_IMAGE}:${DOCKER_TAG}..."
                    docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                '''
            }
        }
    }
}

