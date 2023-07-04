pipeline {
  agent none
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
  }
  stages {
    stage('Initialize') {
      steps {
        script {
          try {
            def dockerHome = tool 'docker'
            env.PATH = "${dockerHome}/bin:${env.PATH}"
            node {
              // Your build steps here
            }
          } catch (Exception e) {
            echo "No agents with the 'docker' label are available. Falling back to any available agent."
            node {
              // Your build steps here
            }
          }
        }
      }
    }
    stage('Build') {
      agent any
      steps {
        sh 'docker build -t bratvagzp/jenkins-docker-hub .'
      }
    }
    stage('Login') {
      agent any
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
      }
    }
    stage('Push') {
      agent any
      steps {
        sh 'docker push bratvagzp/jenkins-docker-hub'
      }
    }
  }
  post {
    always {
      sh 'docker logout'
    }
  }
}

