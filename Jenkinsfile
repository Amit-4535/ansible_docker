pipeline {
    agent any

    environment {
        // DockerHub credentials (saved in Jenkins Credentials store)
        DOCKER_HUB_USER = credentials('dockerhub-user')
        DOCKER_HUB_PASS = credentials('dockerhub-pass')
    }

    stages {
        stage('Checkout Repo') {
            steps {
                git branch: 'master', url: 'https://github.com/Amit-4535/ansible_docker.git'
            }
        }

        stage('Copy Playbook to Ansible Master') {
            steps {
                sshagent(['ansible-ssh']) {
                    sh '''
                        echo "📂 Copying playbook to Ansible Master..."
                        ssh -o StrictHostKeyChecking=no root@34.229.120.195 "mkdir -p /opt/playbooks"
                        scp -o StrictHostKeyChecking=no apache2.yml root@34.229.120.195:/opt/playbooks/
                    '''
                }
            }
        }

        stage('Run Playbook on Ansible Master') {
            steps {
                sshagent(['ansible-ssh']) {
                    sh '''
                        echo "🚀 Running playbook from Ansible Master..."
                        ssh -o StrictHostKeyChecking=no root@34.229.120.195 "
                            ansible-playbook -i /etc/ansible/hosts /opt/playbooks/apache2.yml
                        "
                    '''
                }
            }
        }

        stage('Commit & Push Container from Managed Node') {
            steps {
                sshagent(['ansible-ssh']) {
                    sh '''
                        echo "📦 Committing running container on Managed Node..."
                        ssh -o StrictHostKeyChecking=no root@172.31.21.93 "
                            docker commit nginx_container amit4535/nginx-custom:v1 &&
                            echo '🔑 Logging into DockerHub...' &&
                            docker login -u ${DOCKER_HUB_USER} -p ${DOCKER_HUB_PASS} &&
                            docker push amit4535/nginx-custom:v1
                        "
                    '''
                }
            }
        }
    }
}

