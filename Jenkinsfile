pipeline {
    agent any
    environment {
        DOCKERHUB_USER = credentials('dockerhub_user')
        DOCKERHUB_PASSWORD = credentials('dockerhub_password')
    }
    stages {
        stage("BuildTests") {
            steps {
                echo 'Building tests...'
                sh '''
                    docker-compose -f docker-compose-tests.yml down
                    docker-compose -f docker-compose-tests.yml build
                '''
            }
        }
        stage("RunTests") {
            steps {
                echo 'Running tests...'
                sh '''
                    docker-compose -f docker-compose-tests.yml up --exit-code-from SumaTest SumaTest
                '''
            }
        }
        stage("Build") {
            steps {
                echo 'Building docker images for deployment...'
                sh '''
                    docker-compose -f docker-compose-dev.yml down
                    docker-compose -f docker-compose-dev.yml build
                '''
            }
        }
        stage("PushBuilds") {
            steps {
                echo "Pushing docker images to DockerHub..."
                sh '''
                    echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USER --password-stdin
                    docker-compose -f docker-compose-dev.yml push
                '''
            }
        }
        stage("DeployDev") {
            steps {
                echo "Deploying to development..."
                sh '''
                    docker-compose -f docker-compose-dev.yml down
                    docker-compose -f docker-compose-dev.yml up -d
                '''
            }
        }
        stage("DeployProd") {
            steps {
                echo 'Deploying to production...'
                sh '''
                    docker-compose -f docker-compose-prod.yml down
                    docker-compose -f docker-compose-prod.yml up -d
                '''
            }
        }
    }
}
