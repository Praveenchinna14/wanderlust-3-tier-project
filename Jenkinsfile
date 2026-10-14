pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    environment {
        COMPOSE_FILE = 'docker-compose.yml'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Praveenchinna14/wanderlust-3-tier-project.git'
            }
        }

        stage('Validate Files') {
            steps {
                sh '''
                    set -eu
                    test -f docker-compose.yml
                    test -f backend/package.json
                    test -f frontend/package.json
                    docker --version
                    docker compose version
                '''
            }
        }

        stage('Validate Compose') {
            steps {
                sh 'docker compose -f "$COMPOSE_FILE" config -q'
            }
        }

        stage('Build Images') {
            steps {
                sh 'docker compose -f "$COMPOSE_FILE" build'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    set -eu
                    docker compose -f "$COMPOSE_FILE" up -d --remove-orphans
                '''
            }
        }

        stage('Verify Containers') {
            steps {
                sh '''
                    docker compose -f "$COMPOSE_FILE" ps
                    docker compose -f "$COMPOSE_FILE" logs --tail=60
                '''
            }
        }
    }

    post {
        success {
            echo 'Wanderlust deployment pipeline completed.'
        }

        failure {
            echo 'Pipeline failed. Check the failed stage and container logs.'
        }
    }
}
