pipeline {
    agent any

    environment {
        SPRING_JAR = "target\\UserManagementAPI-0.0.1-SNAPSHOT.jar"
        MAVEN_PATH = '"C:\\Program Files\\Apache\\maven\\bin\\mvn"'
        JAVA_PATH = '"C:\\Program Files\\Java\\jdk-xx\\bin\\java"'
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Assuming code is already available locally...'
            }
        }

        stage('Build Spring Boot App') {
            steps {
                bat "${MAVEN_PATH} clean install"
            }
        }

        stage('Run Spring Boot in Background') {
            steps {
                bat "start /B ${JAVA_PATH} -jar %SPRING_JAR%"
                bat 'timeout /t 10 /nobreak > NUL'
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
            bat 'taskkill /F /IM java.exe || echo No java process found'
        }
        success {
            echo 'Integration tests passed!'
        }
        failure {
            echo 'Integration tests failed!'
        }
    }
}
