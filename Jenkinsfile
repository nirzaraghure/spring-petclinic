pipeline {
    agent any

    tools {
        jdk 'java17'
        maven 'maven3'
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
                bat 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                bat 'mvn test'
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package') {
            steps {
                bat 'mvn package -DskipTests'
            }
        }

        stage('Deploy') {
            steps {
                bat '''
                echo Stopping old Spring Petclinic if running...
                taskkill /F /IM java.exe || echo No old app running

                echo Starting Spring Petclinic on port 8081...
                REM Ensure port 8081 is free
                netstat -ano | findstr :8081
                REM Run the app in background and log output
                start "" java -Dserver.port=8081 -jar target\\*.jar > target\\app.log 2>&1
                echo Spring Petclinic deployed successfully!
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully! Access the app at http://10.237.153.109:8081'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
