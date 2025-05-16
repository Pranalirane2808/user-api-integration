pipeline {
    agent any

    environment {
        // Adjust these paths if needed
        MAVEN_PATH = '"C:\\Progra~1\\Apache~1\\bin\\mvn.cmd"'
        JAVA_PATH = '"C:\\Progra~1\\Java\\jdk-xx\\bin\\java.exe"'
        SPRING_JAR = "target\\UserManagementAPI-0.0.1-SNAPSHOT.jar"
        TASKKILL_PATH = 'C:\\Windows\\System32\\taskkill.exe'
    }

    stages {
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
            bat "${TASKKILL_PATH} /F /IM java.exe || echo No java process found"
        }
        success {
            echo 'Integration tests passed!'
        }
        failure {
            echo 'Integration tests failed!'
        }
    }
}
