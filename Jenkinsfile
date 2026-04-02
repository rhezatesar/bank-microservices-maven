pipeline {
    agent any

    environment {
        SONAR_URL = "http://sonarqube:9000"
        DB_URL = "jdbc:postgresql://postgres:5432/bankdb"
        DB_USER = "sonar"
        DB_PASS = "sonar123"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/YOUR-USERNAME/bank-microservices-git-lab.git'
            }
        }

        stage('Build') {
            steps {
                echo "Simulate build"
            }
        }

        stage('Sonar Scan') {
            steps {
                withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_AUTH_TOKEN')]) {
                    sh '''
                    docker run --rm                     -v $(pwd):/usr/src                     sonarsource/sonar-scanner-cli                     -Dsonar.projectKey=bank-demo                     -Dsonar.sources=.                     -Dsonar.host.url=$SONAR_URL                     -Dsonar.token=$SONAR_AUTH_TOKEN
                    '''
                }
            }
        }

        stage('DB Migration') {
            steps {
                sh '''
                docker run --rm                 --network jenkins_network                 -v $(pwd)/account-service/db/migration:/flyway/sql                 flyway/flyway                 -url=$DB_URL                 -user=$DB_USER                 -password=$DB_PASS                 migrate
                '''

                sh '''
                docker run --rm                 --network jenkins_network                 -v $(pwd)/transaction-service/db/migration:/flyway/sql                 flyway/flyway                 -url=$DB_URL                 -user=$DB_USER                 -password=$DB_PASS                 migrate
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploy success"
            }
        }
    }
}
