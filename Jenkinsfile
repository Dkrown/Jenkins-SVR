pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git 'git@github.com:Dkrown/Jenkins-SVR.git'
            }
        }

        stage('Build Flask App') {
            steps {
                sh 'docker build -t flask-app ./flask-app'
            }
        }

        stage('Build Node.js App') {
            steps {
                sh 'docker build -t node-app ./node-app'
            }
        }

        stage('Run Containers') {
            steps {
                sh 'docker-compose up -d'
            }
        }

        stage('Verify Containers') {
            steps {
                sh 'docker ps'
            }
        }
    }
}
