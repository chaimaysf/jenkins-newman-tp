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
            alwaysLinkToLastBuild: true,
            keepAll: true,
            reportDir: '.',
            reportFiles: 'rapport.html',
            reportName: 'Newman Report'
        ])
    }
    success {
        emailext(
            subject: "✅ Build SUCCESS - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            body: "Tous les tests ont passé. Rapport disponible : ${env.BUILD_URL}Newman_20Report",
            attachmentsPattern: 'rapport.html',
            to: 'chaimaysf2000@gmail.com'
        )
    }
    failure {
        emailext(
            subject: "❌ Build FAILURE - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            body: "Des tests ont échoué. Voir les logs : ${env.BUILD_URL}console",
            to: 'chaimaysf2000@gmail.com'
        )
    }
}
