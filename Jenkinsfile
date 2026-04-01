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

        stage('Build App Locally') {
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
                    # Ensure target directory exists
                    ssh -o StrictHostKeyChecking=no azureuser@$VM_IP 'mkdir -p $TARGET_DIR'

                    # Sync files to VM, excluding node_modules
                    rsync -av --exclude='node_modules' ./ azureuser@$VM_IP:$TARGET_DIR/

                    # Run install and build on the VM
                    ssh -o StrictHostKeyChecking=no azureuser@$VM_IP '
                        cd $TARGET_DIR
                        npm install --legacy-peer-deps
                        npm run build
                        nohup npm start > app.log 2>&1 &
                    '
                    """
                }
            }
        }
    }
}
