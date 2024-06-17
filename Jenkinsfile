pipeline {
    agent any
    stages {
        stage('Checkout SCM') {
            steps {
                git 'https://github.com/JosueRR/jenkins-demo.git'
            }
        }
        stage('Build') {
            steps {
                script {
                    sh 'docker-compose -f docker-compose.yml build'
                }
            }
        }
        stage('Test') {
            steps {
                echo 'Running tests'
                script {
                    sh 'docker-compose -f docker-compose.yml up -d SumaTest'
                    sh 'docker-compose -f docker-compose.yml run --rm SumaTest npm test'
                    sh 'docker-compose -f docker-compose.yml down'
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying'
                script {
                    sh 'docker-compose -f docker-compose.yml up -d --force-recreate Suma'
                    sh 'docker-compose -f docker-compose.yml up -d --force-recreate SitioWeb'
                }
            }
        }
    }
}
