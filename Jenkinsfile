pipeline {
    agent any

    environment {
        DOCKER_USER = 'dakyh'
        BACKEND_IMAGE = "${DOCKER_USER}/filrouge-backend"
        FRONTEND_IMAGE = "${DOCKER_USER}/filrouge-frontend"
        MIGRATE_IMAGE = "${DOCKER_USER}/filrouge-db"
    }

    stages {
        stage('Cloner le dépôt') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/dakyh/odc.git'
            }
        }

        /*
        stage('Analyse SonarQube') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    bat """
                        C:\\Users\\hp\\Desktop\\sonar-scanner\\bin\\sonar-scanner.bat ^
                        -Dsonar.projectKey=mben ^
                        -Dsonar.sources=. ^
                        -Dsonar.projectName=ODC ^
                        -Dsonar.sourceEncoding=UTF-8
                    """
                }
            }
        }
        */

        stage('Build des images') {
            steps {
                bat "docker build -t ${env.BACKEND_IMAGE}:latest ./Backend-main/odc"
                bat "docker build -t ${env.FRONTEND_IMAGE}:latest ./Frontend-main"
                bat "docker build -t ${env.MIGRATE_IMAGE}:latest ./Backend-main/odc"
            }
        }

        stage('Push des images sur Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'filRo', url: ""]) {
                    bat "docker push ${env.BACKEND_IMAGE}:latest"
                    bat "docker push ${env.FRONTEND_IMAGE}:latest"
                    bat "docker push ${env.MIGRATE_IMAGE}:latest"
                }
            }
        }

        stage('Déploiement local avec Docker Compose') {
            steps {
                bat """
                    docker rm -f BackendCont || exit 0
                    docker rm -f FrontendCont || exit 0
                    docker-compose down || exit 0
                    docker-compose pull
                    docker-compose up -d --build
                """
            }
        }
    }

    /*
    post {
        success {
            mail to: 'nayoh.diop@gmail.com',
                 subject: "✅ Déploiement réussi",
                 body: "L'application a été déployée avec succès."
        }
        failure {
            mail to: 'nayoh.diop@gmail.com',
                 subject: "❌ Échec du déploiement",
                 body: "Une erreur s’est produite pendant le pipeline Jenkins."
        }
    }
    */
}
