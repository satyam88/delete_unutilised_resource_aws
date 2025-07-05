pipeline {
    agent any

    parameters {
        string(
            name: 'AWS_DEFAULT_REGION',
            defaultValue: 'us-east-1',
            description: 'AWS region where the script will run'
        )
    }

    environment {
        // Use the parameter as the environment variable
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
                    pip install boto3
                '''
            }
        }

        stage('Run Script') {
            steps {
                sh '''
                    . venv/bin/activate
                    echo "🔹 Using AWS region: $AWS_DEFAULT_REGION"
                    python delete_unused_ebs_volume_accross_regions.py
                '''
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
