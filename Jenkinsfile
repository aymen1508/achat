pipeline {
    agent any
    
    environment {
        SONARQUBE_SCANNER_HOME = tool 'SonarScanner' // Use the name of your SonarQube scanner tool
    }

    tools {
        maven "Maven"
    }

    stages {
        stage('Build') {
            steps {
                script {
                    echo "Building..."
                    sh 'mvn clean install'
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    echo "Testing..."
                    sh 'mvn test'
                }
            }
        }
        stage('SonarQube Analysis') {
            withSonarQubeEnv() {
                sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=jenkins-tuto -Dsonar.projectName='jenkins-tuto'"
            }
        }
    }
}
