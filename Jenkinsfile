pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/W1castanedal/adoptaya.git'
            }
        }

        stage('Build containers') {
            steps {
                sh 'docker compose down || true'
                sh 'docker compose up -d --build'
            }
        }

        stage('Check services') {
            steps {
                sh 'docker ps'
                sh 'curl -I http://localhost:80 || true'
            }
        }
    }

    post {
        always {
            echo "✅ Pipeline finalizado. Revisa los contenedores activos."
        }
    }
}
