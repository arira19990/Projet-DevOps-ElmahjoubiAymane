pipeline {
    agent any

    tools {
        maven 'Maven-3.9.11'
    }

    environment {
        SLACK_WEBHOOK = credentials('slack-webhook')   // <── Ton credential
    }

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

        stage('Tests') {
            steps {
                dir('devops-app') {
                    sh 'mvn test'
                }
            }
        }

        stage('Archive') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                archiveArtifacts artifacts: 'devops-app/target/*.jar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'Build + Tests + Archive réussis'

            // Slack notification via Webhook
            sh """
            curl -X POST -H 'Content-type: application/json' \
            --data '{\"text\":\"✔ SUCCESS : Build + Tests validés et artefact archivé !\"}' \
            $SLACK_WEBHOOK
            """
        }

        failure {
            echo 'Échec du pipeline'

            // Slack notification via Webhook
            sh """
            curl -X POST -H 'Content-type: application/json' \
            --data '{\"text\":\"❌ FAILURE : Build ou Tests ont échoué\"}' \
            $SLACK_WEBHOOK
            """
        }
    }
}
