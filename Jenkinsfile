pipeline {
    agent {
        docker {
            image 'node:18-alpine'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh '''
                    echo "Building the application..."
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }
        stage('Test') {
            steps {
                sh '''
                    echo "Running tests..."
                    npm test
                    test -f build/index.html
                '''
            }
        }
    }
}