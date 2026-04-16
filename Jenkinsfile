pipeline {
    agent any

    environment {
        AWS_REGION = "ap-south-1"
        AWS_ACCOUNT_ID = "739951718542"   // change this
        REPO_NAME = " vinay-python-repo"
        IMAGE_NAME = "sample_python"
         IMAGE_TAG = "latest"
         ECR_REPO = "vinay-python-repo"
        ECR_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${REPO_NAME}"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/utlavinaykumar/sample-python-app.git'
            }
        }

        stage('Verify AWS Role') {
            steps {
                sh 'aws sts get-caller-identity'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                    docker build -t ${IMAGE_NAME} .
                """
            }
        }

       stage('Tag Image') {
    steps {
        sh """
            docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${ECR_URI}:${IMAGE_TAG}
            docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${ECR_URI}:latest
        """
    }
}

   stage('Login to ECR') {
    steps {
        sh """
            aws ecr get-login-password --region ${REGION} | \
            docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${REGION}.amazonaws.com
        """
    }
}

        stage('Push to ECR') {
            steps {
                sh """
                    docker push ${ECR_URI}
                    docker push ${ECR_URI}:latest
                """
            }
        }
    }

    post {
        success {
            echo "✅ Image pushed to AWS ECR: ${ECR_URI}"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}
