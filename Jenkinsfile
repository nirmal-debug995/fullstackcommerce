pipeline {
    agent any

    environment {
        VM_IP = "20.244.9.198"
        TARGET_DIR = "/home/azureuser/commerce-app"
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
                    # Create target directory on VM if it doesn't exist
                    ssh -o StrictHostKeyChecking=no azureuser@$VM_IP 'mkdir -p $TARGET_DIR'

                    # Copy all files to VM
                    scp -r ./* azureuser@$VM_IP:$TARGET_DIR

                    # Navigate to target dir, install dependencies, build, and start app
                    ssh -o StrictHostKeyChecking=no azureuser@$VM_IP << 'EOF'
                    cd $TARGET_DIR
                    npm install --legacy-peer-deps
                    npm run build
                    nohup npm start > app.log 2>&1 &
                    EOF
                    """
                }
            }
        }
    }
}
