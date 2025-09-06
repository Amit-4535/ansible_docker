pipeline {
  agent any

  environment {
    ANSIBLE_MASTER = '34.229.120.195'     // Ansible master IP
    REMOTE_DIR     = '/opt/playbooks'     // directory on Ansible master
    REMOTE_USER    = 'root'               // currently using root for SSH
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
            set -e
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
  }
}

