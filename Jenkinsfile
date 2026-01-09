pipeline {
    agent any

    tools {
        jdk 'java17'
        maven 'maven3'
    }

    environment {
        IMAGE_NAME = "spring-petclinic"
        CONTAINER_NAME = "petclinic"
        APP_PORT = "8081"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/nirzaraghure/spring-petclinic.git'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean compile -Dcheckstyle.skip=true'
            }
        }

        stage('Test') {
            steps {
                bat 'mvn test -Dcheckstyle.skip=true'
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package') {
            steps {
                bat 'mvn package -DskipTests -Dcheckstyle.skip=true'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME%:latest .'
            }
        }

        stage('Deploy Using Docker') {
            steps {
                bat '''
                echo Stopping existing container if any...
                docker stop %CONTAINER_NAME% || echo No container running
                docker rm %CONTAINER_NAME% || echo No container exists

                echo Running Spring Petclinic container...
                docker run -d -p %APP_PORT%:8080 --name %CONTAINER_NAME% %IMAGE_NAME%:latest

                echo Deployment completed successfully!
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline SUCCESS! Access the app at http://localhost:8081"
        }
        failure {
            echo "Pipeline FAILED! Check Jenkins console logs."
        }
    }
}
