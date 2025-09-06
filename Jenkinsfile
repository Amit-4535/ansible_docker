pipeline {
  agent any

  environment {
    ANSIBLE_MASTER   = '34.229.120.195'
    REMOTE_USER      = 'root'
    REMOTE_DIR       = '/opt/playbooks'
    MANAGED_NODE     = '172.31.21.93'
    CONTAINER_ID     = '7fb9d7b6f39e'
    IMAGE_NAME       = 'amit4535/nginx-custom:v1'

    DOCKER_HUB_USER  = credentials('dockerhub-user')  // DockerHub username
    DOCKER_HUB_PASS  = credentials('dockerhub-pass')  // DockerHub password
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'master', url: 'https://github.com/Amit-4535/ansible_docker.git'
      }
    }

    stage('Copy Playbook to Ansible Master') {
      steps {
        sshagent(credentials: ['ansible-ssh']) {
          sh '''
            set -euo pipefail
            test -f docker_setup.yml

            ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${ANSIBLE_MASTER} "mkdir -p ${REMOTE_DIR}"
            scp -o StrictHostKeyChecking=no docker_setup.yml ${REMOTE_USER}@${ANSIBLE_MASTER}:${REMOTE_DIR}/
          '''
        }
      }
    }

    stage('Run Playbook on Ansible Master') {
      steps {
        sshagent(credentials: ['ansible-ssh']) {
          sh '''
            ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${ANSIBLE_MASTER} \
              ansible-playbook -i /etc/ansible/hosts ${REMOTE_DIR}/docker_setup.yml
          '''
        }
      }
    }

    stage('Commit Container as Image') {
      steps {
        sshagent(credentials: ['ansible-ssh']) {
          sh '''
            ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${MANAGED_NODE} \
              "docker commit ${CONTAINER_ID} ${IMAGE_NAME}"
          '''
        }
      }
    }

    stage('Login to DockerHub') {
      steps {
        sh '''
          echo "${DOCKER_HUB_PASS}" | docker login -u "${DOCKER_HUB_USER}" --password-stdin
        '''
      }
    }

    stage('Push Image') {
      steps {
        sh '''
          docker push ${IMAGE_NAME}
        '''
      }
    }
  }
}

