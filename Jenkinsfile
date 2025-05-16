pipeline {
  agent any

  stages {

    stage('Checkout') {
      steps {
        echo "✅ Étape 1 : Clonage du dépôt"
        git url: 'https://github.com/Emjad1257/TpDevops.git', branch: 'main'
      }
    }

    stage('Build') {
      steps {
        echo "🔧 Étape 2 : Build (simulé ici)"
        sh 'echo "Build terminé."'
      }
    }

    stage('Test') {
      steps {
        echo "🧪 Étape 3 : Tests (simulés ici)"
        sh 'echo "Tests OK."'
      }
    }

    stage('Deploy') {
      steps {
        echo "🚀 Étape 4 : Déploiement (simulé ici)"
        sh 'echo "Déploiement terminé."'
      }
    }
  }

  post {
    success {
      echo '✅✅ Pipeline exécutée avec succès !'
    }
    failure {
      echo '❌ Une erreur est survenue durant la pipeline.'
    }
    always {
      echo '📋 Pipeline terminée.'
    }
  }
}
