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
         withCredentials([file(credentialsId: 'sshKey', variable: 'PEM_FILE')]) {
                    sh '''
                        chmod 400 $PEM_FILE
                        ssh -i $PEM_FILE ubuntu@13.49.39.174 "
                          dir('/var/lib/jenkins/workspace/ProjectApp')
                            sh 'sudo su'
                            sh 'docker compose up -d'
                        "
                    '''
         }
         
        }
      }
    }
  }
}
