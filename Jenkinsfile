pipeline {
    agent any

    environment {
        VM_IP = "20.244.9.198"
    }

    stages {

        stage('Clone Repo') {
            steps {
                checkout scm
            }
        }

        stage('Build App') {
            steps {
                sh '''
                npm install --legacy-peer-deps
                npm run build
                '''
            }
        }

        stage('Deploy to Azure VM') {
            steps {
                sshagent(credentials: ['jenkins_key']) {
                    sh """
                    # Ensure the target directory exists
                    ssh -o StrictHostKeyChecking=no azureuser@$VM_IP 'mkdir -p /var/www/commerce-app'

                    # Copy all files from workspace to VM
                    scp -r ./* azureuser@$VM_IP:/var/www/commerce-app
                    """
                }
            }
        }
    }
}
