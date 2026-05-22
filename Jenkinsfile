node {
    docker.image('ochelini/jenkins-agent-devsecops:latest').inside('--network host') {

        // ✅ Image configuration
        def IMAGE_NAME = "ochelini/demo-app"
        def IMAGE_TAG = "v${env.BUILD_NUMBER}"

        stage('Checkout') {
            checkout scm
        }

        stage('Build Application') {
            sh """
            chmod +x mvnw
            ./mvnw -f app/pom.xml clean package -DskipTests
            """
        }

        stage('Build Docker Image') {
            dir('app') {
                sh """
                docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                """
            }
        }

        stage('Scan Docker Image') {
            sh """
            export TRIVY_CACHE_DIR=\$WORKSPACE/.trivy
            mkdir -p \$TRIVY_CACHE_DIR

            trivy image --severity HIGH,CRITICAL --exit-code 1 ${IMAGE_NAME}:${IMAGE_TAG}
            """
        }

        stage('Push Docker Image') {
            withCredentials([usernamePassword(
                credentialsId: 'dockerhub-creds',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS'
            )]) {
                sh """
                export DOCKER_CONFIG=\$WORKSPACE/.docker
                mkdir -p \$DOCKER_CONFIG

                echo "\$DOCKER_PASS" | docker login -u "\$DOCKER_USER" --password-stdin
                docker push ${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }

        stage('Deploy to Kubernetes') {
            sh """
            export KUBECONFIG=/home/jenkins/.kube/config

            kubectl set image deployment/demo-app demo-app=${IMAGE_NAME}:${IMAGE_TAG} --record

            kubectl rollout status deployment/demo-app
            """
        }

    }
}
