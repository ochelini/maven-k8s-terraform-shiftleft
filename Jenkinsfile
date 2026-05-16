node {
    docker.image('ochelini/jenkins-agent-devsecops:latest').inside {

        stage('Checkout') {
            checkout scm
        }

        stage('Build Application') {
            sh '''
            chmod +x mvnw
            ./mvnw -f app/pom.xml clean package -DskipTests
            '''
        }

        stage('Build Docker Image') {
            dir('app') {
                sh 'docker build -t ochelini/demo-app:latest .'
            }
        }

        stage('Scan Docker Image') {
            sh 'trivy image --severity HIGH,CRITICAL --exit-code 1 ochelini/demo-app:latest'
        }

        stage('Push Docker Image') {
            withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                sh '''
                echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                docker push ochelini/demo-app:latest
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            sh '''
            export KUBECONFIG=/home/jenkins/.kube/config
            kubectl apply -f k8s/
            kubectl rollout status deployment/demo-app
            '''
        }

    }
}
