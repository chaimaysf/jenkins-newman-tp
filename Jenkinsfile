pipeline {
    agent any

    tools {
        nodejs 'NodeJS'
    }

    environment {
        POSTMAN_API_KEY = credentials('postman-api-key')
    }

    stages {
        stage('Install Newman') {
            steps {
                sh 'npm install -g newman newman-reporter-htmlextra'
            }
        }
        stage('Run Tests') {
            steps {
                sh '''
                newman run "https://api.getpostman.com/collections/40596805-6f02b1ee-0a49-4a22-98f3-47448fd8dfba?apikey=${POSTMAN_API_KEY}" \
                  --reporters cli,htmlextra \
                  --reporter-htmlextra-export rapport.html
                '''
            }
        }
    }

    post {
        always {
            publishHTML([
                allowMissing: false,
                reportDir: '.',
                reportFiles: 'rapport.html',
                reportName: 'Newman Report'
            ])
        }
    }
}
