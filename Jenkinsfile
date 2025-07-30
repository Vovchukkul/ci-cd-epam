pipeline {
    agent any

    tools {
        nodejs "node7"
    }

    environment {
        IMAGE_NAME = "node${env.BRANCH_NAME}"
    }

    stages {
        stage("Checkout") {
            steps {
                checkout scm
            }
        }

        stage("Build") {
            steps {
                sh 'npm install'
            }
        }

        stage("Test") {
            steps {
                sh 'npm test || true'
            }
        }

        stage("Build docker image") {
            steps {
                script {
                    env.PORT = (env.BRANCH_NAME == 'main') ? '3000' : '3001'
                }
                sh "docker build -t ${IMAGE_NAME}:v1.0 ."
            }
        }

        stage("Deploy") {
            steps {
                sh """
                    docker ps -a --filter "name=${BRANCH_NAME}" --format "{{.ID}}" | xargs -r docker rm -f
                    docker run -d -p ${PORT}:3000 --name ${BRANCH_NAME} ${IMAGE_NAME}:v1.0
                """
            }
        }
    }
}
