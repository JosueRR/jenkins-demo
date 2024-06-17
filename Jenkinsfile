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
                    docker build -t sumatest -f Dockerfile.test ./microservices/sum
                    docker run --name sumatest sumatest
                    docker rm sumatest
                '''
            }
        }
        stage("RunTests") {
            steps {
                echo 'Running tests...'
                sh '''
                    docker run --name sumatest --rm sumatest
                '''
            }
        }
        stage("Build") {
            steps {
                echo 'Building docker images for deployment...'
                sh '''
                    docker build -t jr/sum:latest ./microservices/sum
                    docker build -t jr/webpage-src:latest ./webpage-src
                '''
            }
        }
        stage("PushBuilds") {
            steps {
                echo "Pushing docker images to DockerHub..."
                sh '''
                    echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USER --password-stdin
                    docker push jr/sum:latest
                    docker push jr/webpage-src:latest
                '''
            }
        }
        stage("DeployDev") {
            steps {
                echo "Deploying to development..."
                sh '''
                    docker stop sum || true
                    docker rm sum || true
                    docker stop webpage-src || true
                    docker rm webpage-src || true
                    docker run -d --name sum -p 8083:8080 jr/sum:latest
                    docker run -d --name webpage-src -p 8082:80 jr/webpage-src:latest
                '''
            }
        }
        stage("DeployProd") {
            steps {
                echo 'Deploying to production...'
                sh '''
                    docker stop sum || true
                    docker rm sum || true
                    docker stop webpage-src || true
                    docker rm webpage-src || true
                    docker run -d --name sum -p 8083:8080 jr/sum:latest
                    docker run -d --name webpage-src -p 8082:80 jr/webpage-src:latest
                '''
            }
        }
    }
}
