pipeline{
    agent any
    tools {
            nodejs "node"
    }
    environment {
        BRANCH_NAME = "${env.BRANCH_NAME}"
        IMAGE_TAG = "${env.BRANCH_NAME == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'}"
        PORT = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
    }
    stages{
        stage('Checkout'){
            steps{
                checkout scm
            }
        }
        stage('Install dependencies'){
            steps{
                sh 'npm install'
            }
        }
        stage("run tests") {
            steps{
                sh 'npm test'
            }
        }
        stage('Build image'){
            steps{
                sh "docker build -t ${IMAGE_TAG} ."
            }
        }
        stage ('stop previous container'){
            steps{
                sh """

                    docker ps -q --filter "ancestor=${IMAGE_TAG}" | xargs -r docker stop
                    docker ps -aq --filter "ancestor=${IMAGE_TAG}" | xargs -r docker rm
                """
            }
        }
        stage ('Run container'){
            steps{
                script{
                    docker.run "${IMAGE_TAG}"
                }
            }
        }
    }    
}
