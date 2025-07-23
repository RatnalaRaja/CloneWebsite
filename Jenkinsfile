pipeline {
    agent any


    environment {
        DOCKER_IMAGE = 'your-dockerhub-username/your-repo-name'
        DOCKER_CREDENTIALS_ID = 'dc193b6d-fa49-4fc5-a1d7-ca1ac1b3980f'  // Jenkins Credentials ID
        SONARQUBE_ENV = 'SonarAPI'                  // Jenkins SonarQube server config name
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Code Quality Check') {
            steps {
                script {
                    withSonarQubeEnv("${SONARQUBE_ENV}") {
                        // For static websites, scan HTML/JS/CSS files. Assumes you have sonar-project.properties
                        sh 'sonar-scanner'
                    }
                }
            }
        }

        stage('Wait for SonarQube Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:latest ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_CREDENTIALS_ID}") {
                        sh "docker push ${DOCKER_IMAGE}:latest"
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Check logs."
        }
    }
}
