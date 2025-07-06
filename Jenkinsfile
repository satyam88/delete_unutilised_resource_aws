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
                    pip install boto3
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
                        . venv/bin/activate
                        echo "🔹 Using AWS region: $AWS_DEFAULT_REGION"
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
