pipeline {
    agent any

    tools {
        jdk 'jdk17'
    }

    environment {
        NVD_API_KEY = credentials('nvd-api-key')
    }

    options {
        timestamps()
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Verify') {
            steps {
                dir('app') {
                    sh '''
                        chmod +x ../mvnw
                        ../mvnw clean verify
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Build and verification succeeded'
        }
        failure {
            echo '❌ Build failed — check logs'
        }
        always {
            archiveArtifacts artifacts: 'app/target/*.jar', fingerprint: true
        }
    }
}
