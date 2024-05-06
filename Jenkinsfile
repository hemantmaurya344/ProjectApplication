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
            sh ' echo "version: "2"
              services:
                mysqldb:
                  image: mysql:8-oracle
              
                  networks:
                    - springmysql-net
                  environment:
                    - MYSQL_ROOT_PASSWORD=1
                    - MYSQL_DATABASE=hobbie_backend_db
              
                spring-backend:
                  image: spring-backend:1
                  ports:
                    - "1010:1010"
                  environment:
                    - spring.datasource.url=jdbc:mysql://mysqldb:3306/hobbie_backend_db?useSSL=false&serverTimezone=Europe/Paris
                  networks:
                    - springmysql-net
                  depends_on:
                    - mysqldb
              
                spring-frontend:
                  image: spring-backend:1
                  ports:
                    - "4200:4200"
          
              networks:
                springmysql-net: 
              " > docker-compose.yml
            '
          
            sh 'docker compose up -d'
        }
      }
    }
  }
}
