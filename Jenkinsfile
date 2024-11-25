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
            steps {
                withSonarQubeEnv('sonar-base') { // Pass the name of your SonarQube server instance
                    sh "mvn clean verify sonar:sonar -Dsonar.projectKey=achat Dsonar.projectName='achat' -Dsonar.host.url=http://sonarqube:9000 -Dsonar.token=sqp_1a12dc0afb15e8b02aa2176a314f919d2ad51d53 "                
                }
            }
        }
    }
}
