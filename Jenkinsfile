pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        S3_BUCKET = 'my-react-prod'
        CLOUDFRONT_DIST_ID = 'E1Q0APOLXIH64N'
    }

    stages {

        stage('Checkout') {
            steps {
                git credentialsId: 'github-ssh',
                    url: 'git@github.com:your-username/react-advanced-dashboard.git'
            }
        }

        stage('Install') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Deploy to S3') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-creds'
                ]]) {
                    sh '''
                    aws s3 sync dist/ s3://$S3_BUCKET --delete
                    '''
                }
            }
        }

        stage('Invalidate Cache') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-creds'
                ]]) {
                    sh '''
                    aws cloudfront create-invalidation \
                    --distribution-id $CLOUDFRONT_DIST_ID \
                    --paths "/*"
                    '''
                }
            }
        }
    }
}

