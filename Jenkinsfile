pipeline {
  agent {
    docker {
        image 'hemantmaurya344/docker-maven:v1'
        args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
    }
  }
  stages {
    stage('Build and Test') {
      steps {
         dir('spring-backend'){
            sh 'mvn clean package'
         }
      }
    }
    stage('Build and Push Docker Image') {
      environment {
        DOCKER_IMAGE = "hemantmaurya344/spring-backend:${BUILD_NUMBER}"
        REGISTRY_CREDENTIALS = credentials('docker-cred')
      }
      steps {
        script {
          dir('spring-backend'){
              sh 'docker build -t ${DOCKER_IMAGE} .'
              }
           }
        }
      }
    stage('Deploy'){
      steps{
        script{
             sshagent(credentials: ['ssh-credentials-id']) {
                  sh '''
                      [ -d ~/.ssh ] || mkdir ~/.ssh && chmod 0700 ~/.ssh
                      ssh-keyscan -t rsa,dsa example.com >> ~/.ssh/known_hosts
                      ssh user@example.com ...
                  '''
            }
          sh 'docker compose up -d'
        }
      }
    }
  }
}
