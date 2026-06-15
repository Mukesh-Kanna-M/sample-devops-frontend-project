pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
        S3_BUCKET = 'front-demo-project'
        CLOUDFRONT_DISTRIBUTION_ID = 'E3L6QXK7PZEALF'
    }

    stages {
        stage('Clone Frontend Repo') {
            steps {
                git branch: 'master', 
                credentialsId: 'github-cred', 
                url: 'https://github.com/Mukesh-Kanna-M/sample-devops-frontend-project.git'
            }
        }

        stage('Install & Build') {
            steps {
                sh '''
                
                rm -rf node_modules package-lock.json

                npm install --legacy-peer-deps || true

                npm run build
                
                '''
            }
        }

        stage('Deploy to S3') {
            steps {
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws-creds-personal',  secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh '''
                    aws s3 sync ./out s3://$S3_BUCKET --delete
                    '''
                }
            }
        }

        stage('Invalidate CloudFront') {
            steps {
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws-creds-personal', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh '''
                    aws cloudfront create-invalidation \
                      --distribution-id $CLOUDFRONT_DISTRIBUTION_ID \
                      --paths "/*"
                    '''
                }
            }
        }
    }
}
