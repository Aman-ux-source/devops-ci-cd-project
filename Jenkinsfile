pipeline {
    agent any

    tools {
        maven 'maven3'
    }

    environment {
        IMAGE_NAME = "amanuxsource/devops-app"
        IMAGE_TAG  = "1.0"
    }

    stages {

        stage('Clone Code') {
            steps {
                git branch: 'master',
                url: 'https://github.com/Aman-ux-source/devops-ci-cd-project.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
        withSonarQubeEnv('sonarqube') {
            sh 'mvn clean verify sonar:sonar'
'
        }
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'docker login -u $DOCKER_USER -p $DOCKER_PASS'
                    sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker rm -f devops-app || true
                docker run -d -p 8080:8080 --name devops-app $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }
    }
}
