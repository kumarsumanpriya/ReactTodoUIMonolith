pipeline{
    agent any 

    environment {
        VERCEL_TOCKEN = credentials('vercel_tocken')
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

        stage('Deploy') {
            steps {
                bat 'npx vercel --prod --yes --token=%VERCEL_TOKEN%'
            }

        }
    }
}
    