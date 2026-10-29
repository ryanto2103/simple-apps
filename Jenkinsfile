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
                sh '''
                cd apps
                npm install
                '''
            }
        }

        stage('Testing') {
            steps {
                sh '''
                cd apps
                chmod u+x node_modules/.bin/jest
                chmod u+x node_modules/jest/bin/jest.js
                npm run test:coverage
                '''
            }
        }

        stage('Code Review') {
            steps {
                withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                    sh '''
                    cd apps
                    sonar-scanner \
                        -Dsonar.projectKey=simple-apps \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=http://172.23.13.89:9000 \
                        -Dsonar.login=$SONAR_TOKEN
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                withCredentials([
                    string(credentialsId: 'db-name',           variable: 'DB_NAME'),
                    string(credentialsId: 'db-user',           variable: 'DB_USER'),
                    string(credentialsId: 'db-pass',           variable: 'DB_PASS'),
                    string(credentialsId: 'db-host-container', variable: 'DB_HOST'),
                    string(credentialsId: 'app-port',          variable: 'APP_PORT')
                ]) {
                    sh '''
cat > .env <<EOF
DB_NAME=${DB_NAME}
DB_HOST=${DB_HOST}
DB_USER=${DB_USER}
DB_PASS=${DB_PASS}
APP_PORT=${APP_PORT}
EOF
                    docker compose up --build -d
                    '''
                }
            }
        }
    }
}
