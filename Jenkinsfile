pipeline {
  agent any
  options { timestamps() }
  environment {
    PROJECT_DIR = '/home/wagner/wp-docker'
    COMPOSE     = 'docker compose'  // usamos compose v2 (subcomando)
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
        sh 'ls -la'
      }
    }

    stage('Compose Down (WP only)') {
      steps {
        sh '''
          cd ${PROJECT_DIR}
          # Baja SOLO WP para no tocar el Jenkins externo
          ${COMPOSE} rm -sf nginx wordpress db || true
          ${COMPOSE} down --remove-orphans || true
        '''
      }
    }

    stage('Compose Up (WP only)') {
      steps {
        sh '''
          cd ${PROJECT_DIR}
          # Levanta solo estos servicios (evita crear otro "jenkins")
          ${COMPOSE} up -d --build db wordpress nginx
          ${COMPOSE} ps
        '''
      }
    }

    stage('Smoke Check') {
      steps {
        sh '''
          docker ps --format "table {{.Names}}\\t{{.Status}}\\t{{.Ports}}"
          # Comprobación rápida HTTP local
          (curl -I http://localhost || true)
        '''
      }
    }
  }
  post {
    success { echo '✅ Despliegue OK' }
    failure { echo '❌ Falló el despliegue' }
    always  { sh 'docker ps --format "table {{.Names}}\\t{{.Status}}\\t{{.Ports}}"' }
  }
}
