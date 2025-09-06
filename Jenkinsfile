pipeline {
    agent any

    environment {
        ANSIBLE_MASTER = "172.31.21.93"        // Ansible master IP
        REMOTE_USER = "root"                   // Or "ansible" if that user works
        REMOTE_DIR = "/home/${REMOTE_USER}/playbooks"

        DOCKER_HUB_REPO = "amit4535/nginx-custom"
        IMAGE_TAG = "v1"
        CONTAINER_NAME = "nginx_container"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/Amit-4535/ansible_docker.git'
            }
        }

        stage('Copy Playbook to Ansible Master') {
            steps {
                sshagent (credentials: ['ansible-ssh']) {
                    sh '''#!/bin/bash
                    set -euo pipefail
                    echo "📂 Copying playbook to Ansible Master..."
                    
                    ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${ANSIBLE_MASTER} "mkdir -p ${REMOTE_DIR}"
                    scp -o StrictHostKeyChecking=no docker_setup.yml ${REMOTE_USER}@${ANSIBLE_MASTER}:${REMOTE_DIR}/
                    '''
                }
            }
        }

        stage('Run Playbook on Ansible Master') {
            steps {
                sshagent (credentials: ['ansible-ssh']) {
                    sh '''#!/bin/bash
                    set -euo pipefail
                    echo "▶️ Running playbook on Ansible Master..."
                    
                    ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${ANSIBLE_MASTER} "ansible-playbook ${REMOTE_DIR}/docker_setup.yml"
                    '''
                }
            }
        }

        stage('Commit Container as Image') {
            steps {
                sshagent (credentials: ['ansible-ssh']) {
                    sh '''#!/bin/bash
                    set -euo pipefail
                    echo "📦 Committing running container ${CONTAINER_NAME} as new image..."
                    
                    ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${ANSIBLE_MASTER} "docker commit ${CONTAINER_NAME} ${DOCKER_HUB_REPO}:${IMAGE_TAG}"
                    '''
                }
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-user', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''#!/bin/bash
                    set -euo pipefail
                    echo "🔑 Logging in to DockerHub..."
                    
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Push Image') {
            steps {
                sh '''#!/bin/bash
                set -euo pipefail
                echo "🚀 Pushing image to DockerHub..."
                
                docker push ${DOCKER_HUB_REPO}:${IMAGE_TAG}
                '''
            }
        }
    }
}

