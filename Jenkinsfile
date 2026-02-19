pipeline {
    agent any
    environment {
        APP_NAME = "my-app"
		NODE_JS_BIN = "/var/lib/jenkins/tools/jenkins.plugins.nodejs.tools.NodeJSInstallation/node/bin"
        PATH = "${NODE_JS_BIN}:${env.PATH}"
    }
    tools {
        nodejs "node" 
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
                sh '''
                    npm install
                    pm2 --version || npm install -g pm2
                '''
            }
        }

        stage('Deploy with PM2') {
            steps {
                // Check if the process is running; restart if it is, start if it isn't
                sh '''
                    export PATH=${NODE_JS_BIN}:$PATH
            		PM2_BIN="${NODE_JS_BIN}/pm2"

            		echo "Using pm2 at: $PM2_BIN"
            		$PM2_BIN --version

            		if $PM2_BIN show ${APP_NAME} > /dev/null 2>&1; then
                		echo "App is running, restarting..."
                		$PM2_BIN restart ${APP_NAME} --update-env
            		else
                		echo "App not found, starting fresh..."
                		$PM2_BIN start app.js --name ${APP_NAME}
            		fi

            		$PM2_BIN save
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
			sh 'pm2 status'
        }
        failure {
            echo 'Deployment Failed. Check logs.'
        }
    }
}
