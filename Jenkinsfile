pipeline {
    agent any
    tools {
        maven 'maven' // Sử dụng Maven đã cài thủ công
    }
    environment {
        IMAGE_TAG = "latest"
        SONAR_HOST_URL = "http://localhost:9000" // Thay bằng URL SonarQube của bạn
        SONAR_LOGIN = credentials('sonar-token') // ID token SonarQube trong Jenkins
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/CaoDuyTung86/4-8.git'
            }
        }
        stage('Reset Build Status') {
            steps {
                bat 'if exist "be-fintrack-master\\target" rmdir /s /q "be-fintrack-master\\target"'
                bat 'del /s /q "be-fintrack-master\\.mvn\\watcher.log" 2>nul'
                bat 'mvn -f be-fintrack-master/pom.xml clean --no-snapshot-updates --fail-never'
            }
        }
        stage('Code Analysis - SonarQube') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    // Thêm sonar.projectKey để xác định dự án trong SonarQube
                    bat 'mvn -f be-fintrack-master/pom.xml clean verify sonar:sonar -DskipTests'
                }
            }
        }
         //stage('Quality Gate') {
            //steps {
        //timeout(time: 15, unit: 'MINUTES') {
            //waitForQualityGate abortPipeline: true
                //}
            //}
        //}
        stage('Docker Build') {
            steps {
                bat 'docker-compose -f be-fintrack-master/docker-compose.yml build'
            }
        }
        stage('Deploy Local') {
            steps {
                bat 'docker-compose -f be-fintrack-master/docker-compose.yml down'
                bat 'docker-compose -f be-fintrack-master/docker-compose.yml up -d'
            }
        }
    }
}
