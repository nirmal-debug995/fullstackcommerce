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
                // Use SSH Agent for authentication
                sshagent(credentials: ['jenkins_key']) {
                    // Copy the built app to the remote VM
                    sh """
                    scp -r ./* azureuser@$VM_IP:/var/www/commerce-app
                    """
                }
            }
        }
    }
}
