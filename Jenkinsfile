pipeline {
  agent any
  stages {
    stage ('checkout stage') {
      steps {
        git 'https://github.com/Amit-4535/ansible_docker.git'
      }
    }
    stage ('build stage') {
      steps {
        echo "running build step"
      }
    }
    stage ('test stage') {
      steps {
        echo "running test step"
      }
    }
    stage ('deploy stage') {
      steps {
        echo "running deploy step"
      }
    }
    stage ('creating Folder') {
      steps {
        sh 'mkdir -p project'
      }
    }
    stage ('creating file') {
      steps {
        sh 'echo "hello from the demo file" > project/demo.txt'
      }
    }
   }
}



 
