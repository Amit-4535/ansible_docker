pipeline {
  agent any

  environment {
    ANSIBLE_MASTER = '34.229.120.195'          // <-- change to your Ansible master IP/hostname
    REMOTE_DIR     = '/home/ansible'      // <-- where you want the file to live
    REMOTE_USER    = 'ansible'            // <-- SSH user on Ansible master
  }

  stages {
    stage('Checkout') {
      steps {
        // Use your repo/branch as needed
        git branch: 'master', url: 'https://github.com/Amit-4535/ansible_docker.git'
      }
    }

    stage('Copy playbook to Ansible Master') {
      steps {
        // Use the SSH key stored in Jenkins credentials
        sshagent(credentials: ['ansible-ssh']) {
          sh '''
            set -euo pipefail

            # Ensure the playbook exists in the repo
            test -f apache2.yml

            # Create target dir on remote and copy file
            ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${ANSIBLE_MASTER} "mkdir -p ${REMOTE_DIR}"
            scp -o StrictHostKeyChecking=no apache2.yml ${REMOTE_USER}@${ANSIBLE_MASTER}:${REMOTE_DIR}/

            # (Optional) verify on remote
            ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${ANSIBLE_MASTER} "ls -l ${REMOTE_DIR}/apache2.yml"
          '''
        }
      }
    }
  }
}

