pipeline {
    agent any

    environment {
        REACT_APP_VERSION = "1.0.${BUILD_ID}"
        AWS_DEFAULT_REGION = 'us-east-1'
    }

    stages {

        stage('Deploy to AWS') {
            agent {
                docker {
                    image 'amazon/aws-cli'
                    reuseNode true
                    args "-u root --entrypoint=''"
                }
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'my-AWS-try', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                        aws --version
                        yum install jq -y
                        LATEST_TD_REV=$(aws ecs register-task-definition --cli-input-json file://aws/task-definitions-prod.json | jq '.taskDefinition.revision')
                        echo "Latest Task Definition Revision: $LATEST_TD_REV"
                        aws ecs update-service --cluster jenkins-try1-cluster --service jenkins-try1-cluster-prod-service --task-definition jenkins-try1-cluster-prod:$LATEST_TD_REV
                        aws ecs wait services-stable --cluster jenkins-try1-cluster --services jenkins-try1-cluster-prod-service
                    '''
                }
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }

            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }
    }
}