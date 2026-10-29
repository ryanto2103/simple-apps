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
                sh'''
                docker compose up --build -d
                '''
            }
        }
        
        
    }
}