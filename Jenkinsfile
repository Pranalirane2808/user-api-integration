pipeline {
    agent any

    environment {
        MAVEN_CMD = 'C:\\apache-maven-3.9.6\\bin\\mvn.cmd'
        NEWMAN_CMD = 'C:\\Users\\ranep\\AppData\\Roaming\\npm\\newman.cmd'
        TASKKILL_PATH = 'C:\\Windows\\System32\\taskkill.exe'
    }

    stages {
        stage('Setup Java & Maven') {
            steps {
                bat 'java -version'
                bat "${env.MAVEN_CMD} -version"
            }
        }

        stage('Build Spring Boot App') {
            steps {
                bat "${env.MAVEN_CMD} clean install"
            }
        }

        stage('Run Spring Boot in Background') {
            steps {
                bat 'start /B java -jar target\\user-api.jar'
                sleep time: 10, unit: 'SECONDS'
            }
        }

        stage('Run Integration Tests with Newman') {
            steps {
                bat "${env.NEWMAN_CMD} run tests\\UserAPI.postman_collection.json -e tests\\UserAPI.postman_environment.json"
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            bat "${TASKKILL_PATH} /F /IM java.exe || echo No java process found"
        }

        failure {
            echo 'Integration tests failed!'
        }
    }
}
