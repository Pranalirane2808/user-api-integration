pipeline {
    agent any

    environment {
        MAVEN_HOME = 'C:\\Tools\\apache-maven-3.9.6'
        JAVA_HOME = 'C:\\Tools\\jdk-17'
        SPRING_JAR = 'target\\UserManagementAPI-0.0.1-SNAPSHOT.jar'
        MAVEN_URL = 'https://downloads.apache.org/maven/maven-3/3.9.6/binaries/apache-maven-3.9.6-bin.zip'
        JAVA_URL = 'https://download.java.net/java/GA/jdk17/0d1cfde4252546c6931946de8db48ee2/35/GPL/openjdk-17_windows-x64_bin.zip'
    }

    stages {
        stage('Setup Java & Maven') {
            steps {
                powershell """
                    \$ErrorActionPreference = 'Stop'

                    # Create Tools dir if not exists
                    if (-Not (Test-Path -Path 'C:\\Tools')) {
                        New-Item -ItemType Directory -Path 'C:\\Tools'
                    }

                    # Check Maven
                    if (-Not (Test-Path -Path '${MAVEN_HOME}\\bin\\mvn.cmd')) {
                        Write-Host "Maven not found. Downloading..."
                        Invoke-WebRequest -Uri '${MAVEN_URL}' -OutFile 'C:\\Tools\\maven.zip'
                        Expand-Archive -Path 'C:\\Tools\\maven.zip' -DestinationPath 'C:\\Tools'
                        Remove-Item 'C:\\Tools\\maven.zip'
                    }

                    # Check Java
                    if (-Not (Test-Path -Path '${JAVA_HOME}\\bin\\java.exe')) {
                        Write-Host "Java not found. Downloading..."
                        Invoke-WebRequest -Uri '${JAVA_URL}' -OutFile 'C:\\Tools\\java.zip'
                        Expand-Archive -Path 'C:\\Tools\\java.zip' -DestinationPath 'C:\\Tools\\jdk-17-tmp'
                        Move-Item -Path 'C:\\Tools\\jdk-17-tmp\\jdk-17*' -Destination '${JAVA_HOME}'
                        Remove-Item 'C:\\Tools\\java.zip'
                        Remove-Item 'C:\\Tools\\jdk-17-tmp' -Recurse
                    }
                """
            }
        }

        stage('Build Spring Boot App') {
            steps {
                bat '"%MAVEN_HOME%\\bin\\mvn.cmd" clean install'
            }
        }

        stage('Run Spring Boot in Background') {
            steps {
                bat "start /B %JAVA_HOME%\\bin\\java.exe -jar %SPRING_JAR%"
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
            bat 'C:\\Windows\\System32\\taskkill.exe /F /IM java.exe || echo No java process found'
        }
        success {
            echo 'Integration tests passed!'
        }
        failure {
            echo 'Integration tests failed!'
        }
    }
}
