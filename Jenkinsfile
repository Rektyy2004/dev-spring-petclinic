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
                echo 'Compiling application and running unit tests...'
                bat 'mvnw.cmd clean test'
            }
        }
        
        stage('Package Artifact') {
            steps {
                echo 'Packaging application into JAR...'
                bat 'mvnw.cmd package -DskipTests'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
        
        // --- ADVANCED CD STAGES BELOW ---
        
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
                    // This try-catch safely removes old containers before deploying the new one
                    try {
                        bat 'docker rm -f automated-petclinic'
                    } catch (Exception e) {
                        echo 'No previous container found. Proceeding with deployment...'
                    }
                    // Run the new container on port 8082
                    bat 'docker run -d --name automated-petclinic -p 8082:8080 spring-petclinic:automated'
                }
            }
        }
    }
}