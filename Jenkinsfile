pipeline {
    agent any

    environment {
        SPRING_JAR = "target/UserManagementAPI-0.0.1-SNAPSHOT.jar"
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Assuming code is already available locally...'
            }
        }

        stage('Build Spring Boot App') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Run Spring Boot in Background') {
            steps {
                sh 'nohup java -jar $SPRING_JAR &'
                sh 'sleep 10'
            }
        }

        stage('Run Integration Tests with Newman') {
            steps {
                sh 'newman run UserAPI.postman_collection.json'
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            sh 'pkill -f UserManagementAPI || true'
        }
        success {
            echo 'Integration tests passed!'
        }
        failure {
            echo 'Integration tests failed!'
        }
    }
}
