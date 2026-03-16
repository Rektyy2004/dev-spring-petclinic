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
        
        stage('Package & Archive') {
            steps {
                echo 'Packaging application into JAR...'
                bat 'mvnw.cmd package -DskipTests'
                echo 'Archiving build artifact...'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }
}