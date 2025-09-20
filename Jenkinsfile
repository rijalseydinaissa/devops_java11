pipeline {
    agent any

    tools {
        maven 'Maven-3.9.0'
        jdk 'JDK-17'
    }

    environment {
        DOCKER_IMAGE = "demo-springboot"
        DOCKERHUB_REPO = "issadiol/demo-springboot" // ton repo DockerHub
        RENDER_DEPLOY_HOOK = "https://api.render.com/deploy/srv-d378rfmr433s73ehe220?key=aJVRFosBwPE" // ton deploy hook
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
                sh 'docker tag $DOCKER_IMAGE $DOCKERHUB_REPO:latest'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                    sh 'docker push $DOCKERHUB_REPO:latest'
                    sh 'docker logout'
                }
            }
        }

        stage('Deploy to Render') {
            steps {
                echo 'Déclenchement du déploiement Render...'
                sh 'curl -X POST $RENDER_DEPLOY_HOOK'
            }
        }
    }

    post {
        success {
            echo 'Image poussée et déploiement Render déclenché 🎉'
        }
        failure {
            echo 'Échec du pipeline ❌'
        }
    }
}
