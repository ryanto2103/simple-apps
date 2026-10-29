pipeline {
    agent { label 'devops1-ekoi' }

    stages {
        stage('Pull SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/ryanto2103/simple-apps.git'
            }
        }
        
        stage('Build') {
            steps {
                sh'''
                cd apps
                npm install
                '''
            }
        }
         stage('Set ENV') {
            steps {
                withCredentials ([
                    string(credentialsId:'db-name', variable: 'db-name'),
                    string(credentialsId:'db-user', variable: 'db-user'),
                    string(credentialsId:'db-pass', variable: 'db-pass'),
                    string(credentialsId:'db-host', variable: 'db-host'),
                    string(credentialsId:'app-port', variable: 'app-port'),
                ])
                sh'''
                cat >> .env <EOF \
                DB_NAME=${db-name} \
                DB_HOST=${db-host} \
                DB_USER=${db-user} \
                DB_PASS=${db-pass} \
                APP_PORT=${app-port} \
                EOF

                '''
            }
        }
        
        stage('Testing') {
            steps {
                sh'''
                cd apps
                chmod u+x node_modules/.bin/jest
                chmod u+x node_modules/jest/bin/jest.js
                npm run test:coverage
                '''
            }
        }
        
        stage('Code Review') {
            steps {
                sh'''
                cd apps
                sonar-scanner \
                    -Dsonar.projectKey=simple-apps \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=http://172.23.13.89:9000 \
                    -Dsonar.login=sqp_25eaf72f6941776a119ea7c254c8f07d4adfeb50
                '''
            }
        }
        
         stage('Deploy') {
            steps {
                ithCredentials ([
                    string(credentialsId:'db-name', variable: 'db-name'),
                    string(credentialsId:'db-user', variable: 'db-user'),
                    string(credentialsId:'db-pass', variable: 'db-pass'),
                    string(credentialsId:'db-host-container', variable: 'db-host-container'),
                    string(credentialsId:'app-port', variable: 'app-port'),
                ])
                sh'''
                docker compose up --build -d
                '''
            }
        }
        
        
    }
}