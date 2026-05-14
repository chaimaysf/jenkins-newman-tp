pipeline {
    agent any
    tools { nodejs 'NodeJS' }
    environment {
        POSTMAN_API_KEY = credentials('postman-api-key')
        ENV_TEST = "40596805-5c017c26-00d5-42eb-9c57-f8fcf7f75435"
        ENV_PP = "40596805-9322285f-dfa1-4c25-b40e-b8c59ae94892"
        ENV_PROD = "40596805-a958d3da-1158-4003-9e61-9171ca2e3186"
    }
    stages {
        stage('Select Environment') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'develop') {
                        env.ENV_ID = env.ENV_TEST
                    } else if (env.BRANCH_NAME == 'staging') {
                        env.ENV_ID = env.ENV_PP
                    } else {
                        env.ENV_ID = env.ENV_PROD
                    }
                    echo "Branche : ${env.BRANCH_NAME} → Environnement : ${env.ENV_ID}"
                }
            }
        }
        stage('Install Newman') {
            steps {
                sh 'npm install -g newman newman-reporter-htmlextra'
            }
        }
        stage('Run Tests') {
            steps {
                sh '''
                newman run "https://api.getpostman.com/collections/40596805-6f02b1ee-0a49-4a22-98f3-47448fd8dfba?apikey=${POSTMAN_API_KEY}" \
                  --environment "https://api.getpostman.com/environments/${ENV_ID}?apikey=${POSTMAN_API_KEY}" \
                  --reporters cli,htmlextra \
                  --reporter-htmlextra-export rapport.html
                '''
            }
        }
    }
    post {
        always {
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: true,
                keepAll: true, reportDir: '.', reportFiles: 'rapport.html',
                reportName: 'Newman Report'])
        }
        success {
            emailext(subject: "✅ [${env.BRANCH_NAME}] Build SUCCESS #${env.BUILD_NUMBER}",
                body: "Tests passés sur ${env.BRANCH_NAME}.",
                attachmentsPattern: 'rapport.html',
                to: 'chaimaysf2000@gmail.com')
        }
        failure {
            emailext(subject: "❌ [${env.BRANCH_NAME}] Build FAILURE #${env.BUILD_NUMBER}",
                body: "Tests échoués sur ${env.BRANCH_NAME}.",
                to: 'chaimaysf2000@gmail.com')
        }
    }
}
