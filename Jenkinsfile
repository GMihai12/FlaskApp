pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('aws-access-key')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-key')
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/username/repo-devops-pipeline.git'
            }
        }

        stage('Test') {
            steps {
                sh 'echo "Tests passed!"'
            }
        }

        stage('Terraform Init & Plan') {
            steps {
                dir('terraform') {
                    sh 'terraform init'
                    sh 'terraform plan'
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                dir('terraform') {
                    input message: "Apply changes?"
                    sh 'terraform apply -auto-approve'
                }
            }
        }

        stage('Deploy App') {
            steps {
                sshagent(['ec2-key-jenkins']) {
                    sh 'scp -o StrictHostKeyChecking=no app.py ec2-user@${EC2_PUBLIC_IP}:/home/ec2-user'
                    sh 'ssh -o StrictHostKeyChecking=no ec2-user@${EC2_PUBLIC_IP} "sudo python3 /home/ec2-user/app.py &"'
                }
            }
        }
    }
}
