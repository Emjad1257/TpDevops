pipeline {
  agent any

  environment {
    SONAR_TOKEN = 'squ_d8a038b5c0df0e2b895eb490fe5c3c943a588ac4'
    SONAR_HOST_URL = 'http://172.20.113.236:9000' // Correct pour ton WSL
  }

  stages {
    stage('Checkout') {
      steps {
        echo "🛎 Clonage du dépôt Symfony DevOps"
        git url: 'https://github.com/Emjad1257/TpDevops.git', branch: 'main'
        sh 'ls -la'
      }
    }

    stage('Install Dependencies') {
      steps {
        echo "📦 Installation des dépendances avec Composer"
        sh '''
          docker run --rm \
            -v $(pwd):/app \
            -w /app \
            composer:2.5 \
            composer install --no-interaction --optimize-autoloader
        '''
      }
    }

    stage('Run PHPUnit & Coverage') {
      steps {
        echo "🧪 Lancement des tests avec couverture"
        sh '''
          docker run --rm \
            -v $(pwd):/app \
            -w /app \
            php:8.2-cli \
            sh -c "apt update && apt install -y git unzip && php vendor/bin/phpunit --coverage-clover=coverage.xml || true"
        '''
      }
    }

    stage('SonarQube Analysis') {
      steps {
        echo "📊 Analyse SonarQube avec le scanner Docker"
        withSonarQubeEnv('SonarLocal') {
          sh '''
            docker run --rm \
              -v $(pwd):/usr/src \
              -w /usr/src \
              sonarsource/sonar-scanner-cli:latest \
              sonar-scanner \
                -Dsonar.projectKey=symfony-devops \
                -Dsonar.projectName="Symfony DevOps" \
                -Dsonar.sources=src \
                -Dsonar.php.coverage.reportPaths=coverage.xml \
                -Dsonar.host.url=$SONAR_HOST_URL \
                -Dsonar.login=$SONAR_TOKEN
          '''
        }
      }
    }

    stage('Build Docker Image') {
      steps {
        echo "🐳 Construction de l’image Docker"
        sh 'docker build -t symfony-devops-app .'
      }
    }

    stage('Push to DockerHub') {
      steps {
        echo "📤 Push de l’image sur Docker Hub"
        withCredentials([usernamePassword(
          credentialsId: 'dockerhub-creds',
          usernameVariable: 'DOCKER_USER',
          passwordVariable: 'DOCKER_PASS'
        )]) {
          sh '''
            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
            docker tag symfony-devops-app $DOCKER_USER/symfony-devops-app:latest
            docker push $DOCKER_USER/symfony-devops-app:latest
          '''
        }
      }
    }

    stage('Deploy with Ansible') {
      steps {
        echo "🚀 Déploiement via Ansible"
        sh 'ansible-playbook -i ansible/inventory ansible/playbook-local.yml'
      }
    }
  }

  post {
    always {
      echo '📋 Pipeline terminée.'
    }
    success {
      echo '✅ Pipeline exécutée avec succès.'
    }
    failure {
      echo '❌ Une erreur est survenue durant l’exécution de la pipeline.'
    }
  }
}
