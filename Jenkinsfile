pipeline {
    agent any

    environment {
        MAVEN_HOME = 'C:\\apache-maven-3.9.6' // update path as needed
        PATH = "${env.MAVEN_HOME}\\bin;${env.PATH}"
    }

    stages {
        stage('Setup Java & Maven') {
            steps {
                bat 'java -version'
                bat 'mvn -version'
            }
        }

        stage('Build Spring Boot App') {
            steps {
                bat 'mvn clean install'
            }
        }

        stage('Run Spring Boot in Background') {
            steps {
                bat 'start /B java -jar target\\user-api.jar'
                sleep time: 10, unit: 'SECONDS' // wait for app to start
            }
        }

        stage('Run Integration Tests with Newman') {
            steps {
                bat 'newman run tests/UserAPI.postman_collection.json -e tests/UserAPI.postman_environment.json'
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            bat 'taskkill /F /IM java.exe || echo No java process found'
        }

        failure {
            echo 'Integration tests failed!'
        }
    }
}
