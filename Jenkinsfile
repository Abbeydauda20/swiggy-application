pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        APP_NAME = "swiggy-application-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "abbeydauda"
        DOCKER_PASS = "dockerhub" // Use double quotes for possible interpolation
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}" // No need for + and ""
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
        JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/Abbeydauda20/swiggy-application.git'
            }
        }
        stage("Sonarqube Analysis") {
            steps {
                withSonarQubeEnv('SonarQube-Server') {
                    sh """$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=swiggy-application-CI \
                    -Dsonar.projectKey=swiggy-CICD"""
                }
            }
        }
        stage("Quality Gate") {
            steps {
                script {
                    script {
                        waitForQualityGate(abortPipeline: false, credentialsId: 'SonarQube-Token')
                    }
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
    }
}
