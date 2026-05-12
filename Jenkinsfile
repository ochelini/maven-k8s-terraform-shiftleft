stage('Build Docker Image') {
    dir('app') {
        sh '''
        docker build --pull -t ochelini/demo-app:latest .
        '''
    }
}

stage('Scan Docker Image') {
    sh '''
    trivy image --severity HIGH,CRITICAL --exit-code 1 ochelini/demo-app:latest
    '''
}

stage('Push Docker Image') {
    withCredentials([usernamePassword(
        credentialsId: 'dockerhub-creds',
        usernameVariable: 'DOCKER_USER',
        passwordVariable: 'DOCKER_PASS'
    )]) {
        sh '''
        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
        docker push ochelini/demo-app:latest
        '''
    }
}
