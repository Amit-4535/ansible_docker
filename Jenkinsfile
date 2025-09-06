pipeline {
  agent any

  environment {
    ANSIBLE_MASTER = '34.229.120.195'    // your Ansible master IP/hostname
    REMOTE_DIR     = '/home/ansible'     // target directory on Ansible master
    REMOTE_USER    = 'ansible'           // SSH user on Ansible master
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
          sh '''#!/bin/bash
            set -euo pipefail

            # Ensure the playbook exists in the repo
            test -f apache2.yml

            # Create target dir on remote and copy file
            ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${ANSIBLE_MASTER} "mkdir -p ${REMOTE_DIR}"
            scp -o StrictHostKeyChecking=no apache2.yml ${REMOTE_USER}@${ANSIBLE_MASTER}:${REMOTE_DIR}/

            # Verify on remote
            ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${ANSIBLE_MASTER} "ls -l ${REMOTE_DIR}/apache2.yml"
          '''
        }
      }
    }
  }
}

