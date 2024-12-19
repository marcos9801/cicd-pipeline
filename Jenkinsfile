pipeline {
    agent any
    tools {
        nodejs "node"  // Nombre del Node.js configurado en Jenkins
    }
    environment {
        BRANCH_NAME = "${env.BRANCH_NAME}"  // Nombre de la rama del SCM
        IMAGE_TAG = "nodemain:v1.0"  // Imagen por defecto
        PORT = "3000"  // Puerto por defecto
    }
    stages {
        stage('Set Environment Variables') {
            steps {
                script {
                    // Configurar variables basadas en la rama
                    if (env.BRANCH_NAME != 'main') {
                        echo "yesiiiiiiiiiiiiii"
                        IMAGE_TAG = "nodedev:v1.0"
                        PORT = "3001"
                    }
                }
            }
        }
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
