pipeline {

    agent any

    environment {

        AZURE_CREDENTIALS = credentials('azure-sp')

        AZURE_TENANT_ID = credentials('azure-tenant-id')

        AZURE_SUBSCRIPTION_ID = credentials('azure-subscription-id')

        AZURE_WEBAPP_NAME = 'to-do-app'

        AZURE_RESOURCE_GROUP = 'mk-rg'
    }

    stages {

        stage('Install') {
            steps {
                echo 'Installing dependencies...'

                bat 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo 'Running application tests...'

                bat 'set CI=true && npm test -- --watchAll=false'
            }
        }

        stage('Build') {
            steps {
                echo 'Building React application...'

                bat 'npm run build'
            }
        }

        stage('Package') {
            steps {
                echo 'Creating deployment ZIP...'

                bat '''
                powershell -NoProfile -Command "Compress-Archive -Path 'build\\*' -DestinationPath 'build.zip' -Force"
                '''
            }
        }

        stage('Azure Login') {
            steps {

                echo 'Connecting to Azure...'

                bat '''
                az login --service-principal ^
                    --username "%AZURE_CREDENTIALS_USR%" ^
                    --password "%AZURE_CREDENTIALS_PSW%" ^
                    --tenant "%AZURE_TENANT_ID%"
                '''

                bat '''
                az account set --subscription "%AZURE_SUBSCRIPTION_ID%"
                '''

                bat 'az account show'
            }
        }

        stage('Deploy to Azure') {
            steps {

                echo 'Deploying application to Azure App Service...'

                bat '''
                az webapp deploy ^
                    --resource-group "%AZURE_RESOURCE_GROUP%" ^
                    --name "%AZURE_WEBAPP_NAME%" ^
                    --src-path "build.zip" ^
                    --type zip
                '''
            }
        }

    }

    post {

        success {
            echo 'Application successfully deployed to Azure!'
        }

        failure {
            echo 'Deployment failed. Check Jenkins Console Output.'
        }

    }
}