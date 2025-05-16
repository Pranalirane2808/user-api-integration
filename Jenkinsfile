pipeline {
    agent any

    environment {
        DEFAULT_MAVEN = 'C:\\apache-maven-3.9.6\\bin\\mvn.cmd'
        DEFAULT_NEWMAN = 'C:\\Users\\ranep\\AppData\\Roaming\\npm\\newman.cmd'
        TASKKILL_PATH = 'C:\\Windows\\System32\\taskkill.exe'
    }

    stages {
        stage('Detect Tools') {
            steps {
                script {
                    // Try to find Maven in PATH
                    def mvnPath = bat(script: 'where mvn', returnStdout: true).trim()
                    env.MAVEN_CMD = mvnPath ? mvnPath : DEFAULT_MAVEN

                    // Try to find Newman in PATH
                    def newmanPath = bat(script: 'where newman', returnStdout: true).trim()
                    env.NEWMAN_CMD = newmanPath ? newmanPath : DEFAULT_NEWMAN

                    echo "Using Maven at: ${env.MAVEN_CMD}"
                    echo "Using Newman at: ${env.NEWMAN_CMD}"
                }
            }
        }

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
