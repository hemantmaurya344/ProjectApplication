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
              def dockerImage = docker.image("${DOCKER_IMAGE}")
              docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                  dockerImage.push()
              }
           }
            
        }
      }
    }
    stage('Deploy'){
      steps{
        script{
          sh 'sh /home/ubuntu/scrips/docker.sh'
        }
      }
    }
  }
}
