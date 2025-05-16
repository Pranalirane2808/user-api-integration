pipeline {
    agent any

    environment {
        SPRING_JAR = "target\\UserManagementAPI-0.0.1-SNAPSHOT.jar"
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Assuming code is already available locally...'
                // Jenkins automatically clones the repo when using Pipeline script from SCM
            }
        }

        stage('Build Spring Boot App') {
            steps {
                bat 'mvn clean install'
            }
        }

        stage('Run Spring Boot in Background') {
            steps {
                // Start jar in background (Windows way)
                bat "start /B java -jar %SPRING_JAR%"
                bat 'timeout /t 10 /nobreak'
            }
        }

        stage('Run Integration Tests with Newman') {
            steps {
                bat 'newman run UserAPI.postman_collection.json'
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            // Kill java process running your Spring Boot app
            bat 'taskkill /F /IM java.exe || echo "No java process found"'
        }
        success {
            echo 'Integration tests passed!'
        }
        failure {
            echo 'Integration tests failed!'
        }
    }
}
