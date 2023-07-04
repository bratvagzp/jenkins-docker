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
            agent {
              node {
                label 'docker'
              }
            }
          } catch (Exception e) {
            agent any
            echo "No agents with the 'docker' label are available. Falling back to any available agent."
          }
        }
      }
    }
    stage('Build') {
      steps {
        sh 'docker build -t bratvagzp/jenkins-docker-hub .'
      }
    }
    stage('Login') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
      }
    }
    stage('Push') {
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

