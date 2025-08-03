pipeline {
    agent any

    tools {
        maven 'maven'
    }

    environment {
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Docker Network & SonarQube Start') {
            steps {
                // Đảm bảo network tồn tại trước
                bat 'docker network create fintrack-net || echo network exists'

                // Nếu SonarQube nằm trong docker-compose.yml, khởi động trước
                bat 'docker-compose up -d sonarqube'

                // Chờ SonarQube sẵn sàng (tránh lỗi connect fail)
                bat 'timeout /t 60 /nobreak >nul'
            }
        }

        stage('Code Analysis - SonarQube') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    bat 'mvn -f be-fintrack-master/pom.xml clean verify sonar:sonar -DskipTests'
                }
            }
        }

        stage('Docker Build') {
            steps {
                bat 'docker-compose build'
            }
        }

        stage('Deploy Local') {
            steps {
                bat 'docker-compose down'
                bat 'docker-compose up -d'
            }
        }
    }
}
