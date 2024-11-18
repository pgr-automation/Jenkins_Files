```groovy
pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1' // Replace with your region
        S3_BUCKET = 'lambda-deployment-artifacts' // Replace with your bucket
        LAMBDA_FUNCTION_NAME = 'my-lambda-function' // Replace with your Lambda name
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Pull code from GitHub
                git branch: 'main', url: 'https://github.com/your-repo/your-lambda.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                // Install dependencies (if using Python)
                sh 'pip install -r requirements.txt -t .'
            }
        }

        stage('Zip Code') {
            steps {
                // Create a deployment package
                sh 'zip -r deployment-package.zip *'
            }
        }

        stage('Upload to S3') {
            steps {
                // Upload package to S3
                withAWS(credentials: 'aws-credentials-id', region: "${AWS_REGION}") {
                    s3Upload(file: 'deployment-package.zip', bucket: "${S3_BUCKET}", path: "${LAMBDA_FUNCTION_NAME}/deployment-package.zip")
                }
            }
        }

        stage('Update Lambda') {
            steps {
                // Update Lambda function code
                withAWS(credentials: 'aws-credentials-id', region: "${AWS_REGION}") {
                    sh '''
                    aws lambda update-function-code \
                        --function-name ${LAMBDA_FUNCTION_NAME} \
                        --s3-bucket ${S3_BUCKET} \
                        --s3-key ${LAMBDA_FUNCTION_NAME}/deployment-package.zip
                    '''
                }
            }
        }
    }
}
```