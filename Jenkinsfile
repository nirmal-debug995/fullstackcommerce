pipeline {
    agent any

    environment {
        VM_IP = "<YOUR_VM_IP>"
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
                npm install
                npm run build
                '''
            }
        }

        stage('Deploy to Azure VM') {
            steps {
                sshagent(['azure-vm-ssh-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no azureuser@$VM_IP << EOF
                    cd ~
                    rm -rf commerce
                    git clone https://github.com/vercel/commerce.git
                    cd commerce
                    npm install
                    npm run build
                    nohup npm start > app.log 2>&1 &
                    EOF
                    '''
                }
            }
        }
    }
}
