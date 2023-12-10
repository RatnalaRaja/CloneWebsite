pipeline {
    agent any
    tools {
        // Update 'sonar_scanner' to match the tool name configured in Jenkins
        sonarqube 'sonar_scanner'
    }
    stages {
        stage('SonarQube Analysis') {
            steps {
                script {
                    // Update '/opt/sonarqube/bin/sonar-scanner' to match the actual path
                    withSonarQubeEnv('sonar_scanner') {
                        sh '/opt/sonarqube/bin/sonar-scanner'
                    }
                }
            }
        }
    }
}
