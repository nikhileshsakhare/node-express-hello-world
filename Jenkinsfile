pipeline {
    agent any
    environment {
        APP_NAME = "my-app"
    }

    stages {
        stage('Checkout') {
            steps {
                // Pulls the code from the repo configured in the Jenkins job
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Security Scan (Optional)') {
            steps {
                sh 'npm audit fix || true' 
            }
        }

        stage('Deploy with PM2') {
            steps {
                // Check if the process is running; restart if it is, start if it isn't
                sh '''
                    pm2 describe ${APP_NAME} > /dev/null
                    if [ $? -eq 0 ]; then
                        pm2 restart ${APP_NAME} --update-env
                    else
                        pm2 start app.js --name ${APP_NAME}
                    fi
                    pm2 save
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed. Check logs.'
        }
    }
}
