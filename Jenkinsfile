
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'sindhuuu12/exp5'
        DOCKER_CMD = 'C:\\Users\\Lenovo\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Sindhurani-C/exp-5.git'
            }
        }

        stage('Build Application') {
            steps {
                echo 'Building the application...'
                bat 'dir'
            }
        }

        stage('Check Docker') {
            steps {
                bat '"%DOCKER_CMD%" --version'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '"%DOCKER_CMD%" build -t %DOCKER_IMAGE%:latest .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-new',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    powershell '''
                        Write-Host "Jenkins Windows account:"
                        whoami

                        Write-Host "Docker username: $env:DOCKER_USER"

                        if ([string]::IsNullOrEmpty($env:DOCKER_PASS)) {
                            Write-Host "Password injected: NO"
                            exit 1
                        } else {
                            Write-Host "Password injected: YES"
                        }

                        $env:DOCKER_PASS | & $env:DOCKER_CMD login -u $env:DOCKER_USER --password-stdin

                        if ($LASTEXITCODE -ne 0) {
                            exit $LASTEXITCODE
                        }
                    '''

                    bat '"%DOCKER_CMD%" push %DOCKER_IMAGE%:latest'
                    bat '"%DOCKER_CMD%" logout'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline execution failed!'
        }
    }
}
