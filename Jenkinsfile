pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'nodejs-app'
        GIT_REPO = 'https://github.com/irfankhan47/-Node.js-Project.git'
        DOCKER_USER = 'irfankhan47'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git "${env.GIT_REPO}"
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                    docker stop nodeapp || true
                    docker rm nodeapp || true
                    docker run -d -p 3000:3000 --name nodeapp $DOCKER_IMAGE
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
                    sh '''
                        echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin
                        docker tag $DOCKER_IMAGE $DOCKERHUB_USER/$DOCKER_IMAGE:latest
                        docker push $DOCKERHUB_USER/$DOCKER_IMAGE:latest
                    '''
                }
            }
        }
    }
}
