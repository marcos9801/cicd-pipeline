pipeline {
    agent any
    tools {
        nodejs "node"  // Nombre del Node.js configurado en Jenkins
    }
    environment {
        IMAGE_TAG = "${env.BRANCH_NAME == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'}"  // Imagen por defecto
        PORT = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}" // Puerto por defecto
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Install dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${env.IMAGE_TAG} ."
            }
        }
        stage('Stop Previous Container') {
            steps {
                script {
                    sh """
                    CONTAINER_ID=\$(docker ps -q --filter "ancestor=${env.IMAGE_TAG}")
                    if [ ! -z "\$CONTAINER_ID" ]; then
                        docker stop \$CONTAINER_ID
                        docker rm \$CONTAINER_ID
                    else
                        echo "No containers to stop for image ${env.IMAGE_TAG}"
                    fi
                """
                }
            }
        }
        stage('Run Docker Container') {
            steps {
                sh "docker run -d -p ${env.PORT}:3000 --name my-app ${env.IMAGE_TAG}"
            }
        }
    }
}
