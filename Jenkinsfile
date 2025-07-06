pipeline {
    agent any

    parameters {
        string(
            name: 'AWS_DEFAULT_REGION',
            defaultValue: 'ap-south-1',
            description: 'AWS region where the script will run'
        )
    }

    environment {
        AWS_DEFAULT_REGION = "${params.AWS_DEFAULT_REGION}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install boto3 awscli
                '''
            }
        }

        stage('Run Script') {
            steps {
                withCredentials([[ 
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'my-aws-credentials-id'
                ]]) {
                    sh '''
                        set -e
                        . venv/bin/activate
                        echo "🔹 AWS Region: $AWS_DEFAULT_REGION"
                        echo "🔹 AWS Access Key: $AWS_ACCESS_KEY_ID"
                        
                        echo "🔹 Verifying AWS identity with STS..."
                        aws sts get-caller-identity --region $AWS_DEFAULT_REGION

                        echo "🔹 Running delete script..."
                        python delete_unused_ebs_volume_accross_regions.py
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Script executed successfully!'
        }
        failure {
            echo '❌ Script execution failed!'
        }
    }
}
