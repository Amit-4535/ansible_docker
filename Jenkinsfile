pipeline {
  agent any

  environment {
    ANSIBLE_MASTER = '34.229.120.195'   // <-- Ansible Master IP/hostname
    REMOTE_DIR     = '/opt/playbooks'   // <-- directory for playbooks
    REMOTE_USER    = 'ansible'          // <-- SSH user on Ansible Master
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'master', url: 'https://github.com/Amit-4535/ansible_docker.git'
      }
    }

    stage('Copy playbook to Ansible Master') {
      steps {
        sshagent(credentials: ['ansible-ssh']) {
          sh '''
            # Ensure playbook exists
            test -f apache2.yml

            # Create target dir and copy playbook
            ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${ANSIBLE_MASTER} "mkdir -p ${REMOTE_DIR}"
            scp -o StrictHostKeyChecking=no apache2.yml ${REMOTE_USER}@${ANSIBLE_MASTER}:${REMOTE_DIR}/
          '''
        }
      }
    }

    stage('Run Playbook on Ansible Master') {
      steps {
        sshagent(credentials: ['ansible-ssh']) {
          sh '''
            ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${ANSIBLE_MASTER} "
              ansible-playbook -i /etc/ansible/hosts ${REMOTE_DIR}/apache2.yml
            "
          '''
        }
      }
    }
  }
}

