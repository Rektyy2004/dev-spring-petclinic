pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Retrieving source code from GitHub...'
                git branch: 'main', url: 'https://github.com/Rektyy2004/dev-spring-petclinic.git'
            }
        }
        
        stage('Build & Test') {
            steps {
                echo 'Running unit tests (skipping DB integration tests for CI environment)...'
                bat 'mvnw.cmd clean test -Dtest=!PostgresIntegrationTests,!MySqlIntegrationTests'
            }
        }
        
        // --- ADVANCED CONTINUOUS INSPECTION STAGE ---
        stage('Code Quality Analysis') {
            steps {
                echo 'Sending code to SonarQube for vulnerability scanning...'
                bat 'mvnw.cmd clean verify sonar:sonar -Dsonar.projectKey=petclinic -Dsonar.host.url=http://localhost:9000 -Dsonar.token=squ_613e7e7044cb300aaaa7c092573b7eb211f2a784'
            }
        }
        
        stage('Package Artifact') {
            steps {
                echo 'Packaging application into JAR...'
                bat 'mvnw.cmd package -DskipTests'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo 'Jenkins is building the Docker Image...'
                bat 'docker build -t spring-petclinic:automated .'
            }
        }
        
        stage('Deploy to Production') {
            steps {
                echo 'Jenkins is deploying the container...'
                script {
                    try {
                        bat 'docker rm -f automated-petclinic'
                    } catch (Exception e) {
                        echo 'No previous container found. Proceeding with deployment...'
                    }
                    bat 'docker run -d --name automated-petclinic -p 8082:8080 spring-petclinic:automated'
                }
            }
        }
    }
}