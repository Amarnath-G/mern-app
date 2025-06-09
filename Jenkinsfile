pipeline {
    agent any

    environment {
        DOCKER_HOST_USER = 'ec2-user'                         // or ubuntu, root, etc.
        DOCKER_HOST_IP = '16.170.254.164'
        PRIVATE_KEY = 'private_key'                       // Jenkins Credentials ID (file)
        REPO_PATH = '/home/ec2-user/app1'                    // Path to code on Docker host
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Detect Changes') {
            steps {
                script {
                    def changeLog = currentBuild.changeSets
                    def frontendChanged = false
                    def backendChanged = false

                    for (changeSet in changeLog) {
                        for (entry in changeSet.items) {
                            for (file in entry.affectedFiles) {
                                if (file.path.startsWith("client/")) {
                                    frontendChanged = true
                                } else if (file.path.startsWith("server/")) {
                                    backendChanged = true
                                }
                            }
                        }
                    }

                    env.FRONTEND_CHANGED = frontendChanged.toString()
                    env.BACKEND_CHANGED = backendChanged.toString()
                }
            }
        }

        stage('Build & Restart on Docker Host') {
            steps {
                withCredentials([file(credentialsId: env.PRIVATE_KEY, variable: 'KEY_PATH')]) {
                    script {
                        if (env.FRONTEND_CHANGED == 'true') {
                            sh """
                                ssh -o StrictHostKeyChecking=no -i $KEY_PATH $DOCKER_HOST_USER@$DOCKER_HOST_IP '
                                cd $REPO_PATH &&
                                git pull origin main &&
                                docker-compose build frontend &&
                                docker-compose up -d --no-deps --build client'
                            """
                        }

                        if (env.BACKEND_CHANGED == 'true') {
                            sh """
                                ssh -o StrictHostKeyChecking=no -i $KEY_PATH $DOCKER_HOST_USER@$DOCKER_HOST_IP '
                                cd $REPO_PATH &&
                                git pull origin main &&
                                docker-compose build backend &&
                                docker-compose up -d --no-deps --build server'
                            """
                        }
                    }
                }
            }
        }
    }
}
