pipeline {
    agent { label 'ec2-private-agent' }

    environment {
        DOCKER_IMAGE = 'nodejs-app'
        CONTAINER_NAME = 'nodejs-app-container'
        DOCKERFILE = 'dockerfile'
        RDS_HOSTNAME = credentials('RDS_HOSTNAME')
        RDS_USERNAME = credentials('RDS_USERNAME')
        RDS_PASSWORD = credentials('RDS_PASSWORD')
        RDS_PORT = credentials('RDS_PORT')
        REDIS_HOSTNAME = credentials('REDIS_HOSTNAME')
        REDIS_PORT = credentials('REDIS_PORT')
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/ArwaHazem/jenkins_nodejs_example_deployment.git', branch: 'rds_redis'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t \${DOCKER_IMAGE} -f \${DOCKERFILE} ."
                }
            }
        }

        stage('Remove Existing Container') {
            steps {
                script {
                    def isRunning = sh(script: "docker ps -q --filter name=\${CONTAINER_NAME}", returnStdout: true).trim()
                    if (isRunning) {
                        sh "docker stop \${CONTAINER_NAME}"
                    }
                    def isExisting = sh(script: "docker ps -a -q --filter name=\${CONTAINER_NAME}", returnStdout: true).trim()
                    if (isExisting) {
                        sh "docker rm \${CONTAINER_NAME}"
                    }
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    sh """
                    docker run -d --name \${CONTAINER_NAME} \
                    -p 3000:3000 \
                    -e RDS_HOSTNAME=\${RDS_HOSTNAME} \
                    -e RDS_USERNAME=\${RDS_USERNAME} \
                    -e RDS_PASSWORD=\${RDS_PASSWORD} \
                    -e RDS_PORT=\${RDS_PORT} \
                    -e REDIS_HOSTNAME=\${REDIS_HOSTNAME} \
                    -e REDIS_PORT=\${REDIS_PORT} \
                    \${DOCKER_IMAGE}
                    """
                }
            }
        }
    }
}
