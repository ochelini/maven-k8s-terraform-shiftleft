pipeline {
    agent {
  docker {
    image 'ochelini/jenkins-agent-devsecops:latest'
    args '-u jenkins'
  }
}

    environment {
        // Optional – if credential exists, Dependency-Check will use it
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


       stage('SAST - Semgrep') {
    steps {
        sh 'semgrep --config=auto --severity=ERROR --error'
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

        stage('Trivy Filesystem Scan') {
            steps {
                sh '''
                    trivy fs \
                      --severity HIGH,CRITICAL \
                      --exit-code 1 \
                      --no-progress \
                      .
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Build, verify, and Trivy scan succeeded'
        }
        failure {
            echo '❌ Pipeline failed (build or security gate)'
        }
        always {
            archiveArtifacts artifacts: 'app/target/*.jar', fingerprint: true
        }
    }
}
