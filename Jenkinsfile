pipeline {
    agent any  // This means it will run on any available agent (you can also specify a specific agent)

    environment {
        // Define environment variables
        DEPLOYMENT_DIR = '/var/www/myapp'  // Path to deploy the app on the server
        SERVER_USER = 'ubuntu'               // SSH user for the server
        SERVER_HOST = '172.31.7.246'    // Hostname or IP address of the server
        SSH_KEY = credentials('slave test') // Jenkins credentials for SSH key
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from Git repository
                git url: 'https://github.com/swathi6327/zookeeper.git', branch: 'master'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Install dependencies (e.g., npm for Node.js applications)
                    sh 'npm install'
                }
            }
        }

        stage('Build Application') {
            steps {
                script {
                    // Run build (e.g., using npm, webpack, etc.)
                    sh 'npm run build'  // or any other build command you use
                }
            }
        }

        stage('Package Application') {
            steps {
                script {
                    // Package the application (e.g., create a zip file for deployment)
                    sh 'zip -r app-package.zip ./*'
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                script {
                    // Deploy the application to the server using SCP or rsync
                    sshagent (credentials: ['my-ssh-key']) {
                        sh '''
                            scp app-package.zip $SERVER_USER@$SERVER_HOST:$DEPLOYMENT_DIR
                            ssh $SERVER_USER@$SERVER_HOST "unzip -o $DEPLOYMENT_DIR/app-package.zip -d $DEPLOYMENT_DIR"
                            ssh $SERVER_USER@$SERVER_HOST "npm install --production --prefix $DEPLOYMENT_DIR"
                            ssh $SERVER_USER@$SERVER_HOST "pm2 restart myapp || pm2 start $DEPLOYMENT_DIR/app.js --name myapp"
                        '''
                    }
                }
            }
        }
        
        stage('Post Deployment') {
            steps {
                echo 'Deployment complete!'
                // You can add notifications here (e.g., Slack or email notifications)
            }
        }
    }

    post {
        failure {
            echo 'Deployment failed!'
            // Handle failure (e.g., send notification or perform rollback)
        }
        success {
            echo 'Deployment successful!'
            // Handle success (e.g., send success notification)
        }
    }
}
