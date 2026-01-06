pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                dir('devops-app') {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'devops-app/target/*.jar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'Build et archivage réussis'
        }
        failure {
            echo 'Échec du pipeline'
        }
    }
}
