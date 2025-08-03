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
                bat 'docker network create fintrack-net || echo network exists'

                bat 'docker-compose up -d sonarqube'

                bat 'ping 127.0.0.1 -n 60 >nul'
            }
        }

        stage('Code Analysis - SonarQube') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    bat 'mvn -f be-fintrack-master/pom.xml clean verify sonar:sonar -DskipTests -Dsonar.token=sqa_11761b8921bad598fd04dc35745937f2cde6fdb7'
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
