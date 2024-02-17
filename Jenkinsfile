pipeline {
    agent {label 'Linux-Slave'}

    environment {
        // Define environment variables
        EC2_HOST = '172.31.83.195'
        EC2_USER = 'linux-slave'
        // Assuming you've added your SSH private key to Jenkins' credentials store
        SSH_KEY_ID = 'linux-slave-id'
    }

    stages {
        stage('Checkout') {
            steps {
                // Check out from GitHub
                git url: 'https://github.com/Khurram-iq/LearnApacheDeployment.git', branch: 'main'
            }
        }

        stage("checking files"){
            steps{
                sh "ls -ltr"
                sh "pwd"
            }
        }

        stage("checking ssh connectivity"){
            steps {
                sshagent(credentials: [SSH_KEY_ID]) {
                sh '''
                    [ -d ~/.ssh ] || mkdir ~/.ssh && chmod 0700 ~/.ssh
                    ssh-keyscan -t rsa,dsa ${EC2_HOST} >> ~/.ssh/known_hosts
                    ssh ${EC2_USER}@${EC2_HOST} uname
                '''
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                script {
                    // SSH and SCP commands to transfer files and restart Apache
                    sshagent(credentials: [SSH_KEY_ID]) {
                        // Copy files to the Apache directory on EC2
                        sh "scp -o StrictHostKeyChecking=no -r ${env.WORKSPACE}/* ${EC2_USER}@${EC2_HOST}:/var/www/html"
// Optional: Run any commands on EC2, like setting permissions or restarting Apache
                        //sh "ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} 'systemctl restart apache2'"
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful.'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}