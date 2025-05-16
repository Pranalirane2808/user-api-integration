pipeline {
    agent any

    environment {
        MAVEN_HOME = '"C:\\Program Files\\apache-maven-3.9.9\\bin\\mvn.cmd"'
        PATH = "${env.PATH};C:\\Users\\jenkins\\AppData\\Roaming\\npm;C:\\Windows\\System32"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup Java & Maven') {
            steps {
                bat 'java -version'
                bat "${MAVEN_HOME} -version"
            }
        }

        stage('Build Spring Boot App') {
            steps {
                bat "${MAVEN_HOME} clean package"
            }
        }

        stage('Run Spring Boot in Background') {
            steps {
                bat 'start java -jar target\\UserManagementAPI-0.0.1-SNAPSHOT.jar'
                sleep time: 15, unit: 'SECONDS'
            }
        }

        stage('Run Integration Tests with Newman') {
            steps {
                bat 'newman run tests/user-api.postman_collection.json -e tests/env.postman_environment.json'
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            bat 'C:\\Windows\\System32\\taskkill.exe /F /IM java.exe || echo No java process found'
        }
        failure {
            echo 'Integration tests failed!'
        }
    }
}
