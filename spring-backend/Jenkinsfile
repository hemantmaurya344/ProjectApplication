pipeline {
  agent {
    docker {
      image 'hemantmaurya344/docker-maven:v1'
      args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
    }
  }
  stages {
    stage('Checkout') {
      steps {
        sh 'echo passed'
      }
    }
    stage('Build and Test') {
      steps {
        sh 'mvn clean package'
      }
    }
    stage('Build and Push Docker Image') {
      environment {
        DOCKER_IMAGE = "hemantmaurya344/sample:${BUILD_NUMBER}"
        REGISTRY_CREDENTIALS = credentials('docker-cred')
      }
      steps {
        script {
            sh 'docker build -t ${DOCKER_IMAGE} .'
            def dockerImage = docker.image("${DOCKER_IMAGE}")
            docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                dockerImage.push()
            }
        }
      }
    }
    stage('Deploy to Docker'){
       environment {
        DOCKER_IMAGE = "hemantmaurya344/sample:${BUILD_NUMBER}"
      }
      steps{
        script{
          sh 'docker ps -aqf "name=application" | xargs docker stop | xargs docker rm'
          sh 'docker run --name=application -d -p 1010:1010 $DOCKER_IMAGE'
        }
      }
    }
  }
}
