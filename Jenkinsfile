pipeline {
    agent any

    environment {
        AZURE_CREDENTIALS = credentials('azure-sp')
        AZURE_WEBAPP_NAME = 'to-do-app'
        AZURE_RESOURCE_GROUP = 'mk-rg'
        AZURE_TENANT_ID = credentials('016d954d-31fa-45d0-84d6-35c7eed1cf23')
        AZURE_SUBSCRIPTION_ID = credentials('87aae48e-0c6c-499c-96b5-89d0cca99db2')
    }

    stages {

        stage('Install') {
            steps {
                bat 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo 'Skipping tests - no test script found'
            }
        }

        stage('Build') {
            steps {
                bat 'npm run build'
            }
        }

        stage('Azure Login') {
            steps {
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
                bat '''
                az webapp deploy ^
                    --resource-group "%AZURE_RESOURCE_GROUP%" ^
                    --name "%AZURE_WEBAPP_NAME%" ^
                    --src-path "dist.zip" ^
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
            echo 'Azure deployment failed. Check Jenkins Console Output.'
        }
    }
}