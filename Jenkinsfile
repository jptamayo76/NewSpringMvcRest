pipeline {
    agent any
    stages {
        stage('Inicio') { 
            steps {
                echo "Este es el inicio"
            }
        }
        stage('Build') { 
            steps {
                sh 'mvn -B package' 
                // sh 'mvn -B -DskipTests clean package' 
            }
        }
        stage('Test') { 
            steps {
                sh 'mvn clean verify' 
            }
        }
    }
}