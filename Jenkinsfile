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
                npm install
                npm run build
                '''
            }
        }

        stage('Deploy to Azure VM') {
            steps {
                sshagent(['jenkins-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no azureuser@$VM_IP << EOF
                    cd ~
                    rm -rf commerce
                    git clone https://github.com/nirmal-debug995/fullstackcommerce.git
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
