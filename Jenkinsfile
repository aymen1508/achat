pipeline {
    agent any

    environment {
        // Define environment variables
        SONARQUBE_SCANNER_HOME = tool 'SonarScanner' // Use the name of your SonarQube scanner tool
        DOCKERHUB_CREDENTIALS = credentials('1521bdc5-4c58-4549-a14f-b98c8da7b1dc') // Use the ID of your DockerHub credentials
        NEXUS_CREDENTIALS = credentials('07c9f2c3-2374-4925-8ffd-06071b125a58') // Use the ID of your Nexus credentials
        DOCKERHUB_REPO = 'aymen1508/achat'
    }

    tools {
        maven 'Maven' // Use the name of your Maven tool
    }

    stages {
        // stage('Checkout') {
        //     steps {
        //         // Checkout code from Git repository
        //         git credentialsId: 'd6ce1028-e78c-424b-9df1-ae1d644aa33a','https://github.com/aymen1508/achat'
        //     }
        // }

        stage('Clean') {
            steps {
                // Run mvn clean
                sh 'mvn clean'
            }
        }

        stage('Build Artifact') {
            steps {
                // Construct the artifact
                sh 'mvn package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Run SonarQube analysis
                withSonarQubeEnv('sonar-base') { // Use the name of your SonarQube server instance
                    sh "mvn sonar:sonar -Dsonar.projectKey=achat -Dsonar.projectName='achat' -Dsonar.host.url=http://host.docker.internal:9000 -Dsonar.token=sqp_1a12dc0afb15e8b02aa2176a314f919d2ad51d53"
                }
            }
        }

        stage('Publish to Nexus') {
            steps {
                // Publish the artifact to Nexus
                withCredentials([usernamePassword(credentialsId: 'nexus-credentials', usernameVariable: 'NEXUS_USERNAME', passwordVariable: 'NEXUS_PASSWORD')]) {
                    sh 'mvn deploy -DaltDeploymentRepository=nexus::default::http://host.docker.internal:8081/repository/maven-releases/ -Dnexus.username=$NEXUS_USERNAME -Dnexus.password=$NEXUS_PASSWORD'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build Docker image
                script {
                    def app = docker.build("${env.DOCKERHUB_REPO}:${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                // Push Docker image to DockerHub
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        def app = docker.image("${env.DOCKERHUB_REPO}:${env.BUILD_NUMBER}")
                        app.push()
                    }
                }
            }
        }
    }
}
