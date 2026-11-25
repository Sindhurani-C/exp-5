
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'sindhuuu12/exp5'
        DOCKER_CREDENTIALS = 'dockerhub-new'

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
    Write-Host "Jenkins is running as:"
    whoami

    Write-Host "Docker username received: $env:DOCKER_USER"
    Write-Host "Password injected: $(-not [string]::IsNullOrEmpty($env:DOCKER_PASS))"

    $psi = New-Object System.Diagnostics.ProcessStartInfo
    $psi.FileName = $env:DOCKER_CMD
    $psi.Arguments = "login --username `"$env:DOCKER_USER`" --password-stdin"
    $psi.UseShellExecute = $false
    $psi.RedirectStandardInput = $true
    $psi.RedirectStandardOutput = $true
    $psi.RedirectStandardError = $true

    $process = New-Object System.Diagnostics.Process
    $process.StartInfo = $psi

    [void]$process.Start()
    $process.StandardInput.WriteLine($env:DOCKER_PASS)
    $process.StandardInput.Close()

    $output = $process.StandardOutput.ReadToEnd()
    $errorOutput = $process.StandardError.ReadToEnd()
    $process.WaitForExit()

    Write-Output $output

    if ($process.ExitCode -ne 0) {
        Write-Error $errorOutput
        exit $process.ExitCode
    }
'''
