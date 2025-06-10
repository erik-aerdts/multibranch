pipeline {

  agent any

  options {

    buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')

  }
stages {
        
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
                sh """
                echo "Cleaned Up Workspace For Project"
                """
            }
        }

        stage('Code Checkout') {
          
      steps {
        echo "Checking out source code for branch: ${env.BRANCH_NAME}"
        checkout scm
        }
        }



  stage('HTML Testing') {
    steps {
        sh """
        echo "Running HTML validation with tidy"
        tidy -qe index.html
        """
    }
}

  
                
 stage('Deploy') {
  when {
    anyOf {
      branch 'dev'
      branch 'test'
      branch 'main'
    }
  }
  steps {
    script {
      def envMap = [
        dev : '172.17.1.111',
        test: '172.17.1.112',
        main: '172.17.1.113'  // productie
      ]
      def currentEnv = env.BRANCH_NAME
      def targetHost = envMap[currentEnv]
      def rollbackTriggered = false

      echo "Deploy to: ${currentEnv} (${targetHost})"

      // Alleen voor productie: input-bevestiging
      if (currentEnv == 'main') {
        try {
          timeout(time: 30, unit: 'MINUTES') {
            input(
              message: "Bevestig deployment naar **PRODUKTIE** (${targetHost}). De job wordt automatisch geannuleerd na 30 minuten.",
              ok: "Deploy"
            )
          }
        } catch (err) {
          echo "Input afgebroken of timeout verlopen. Start rollback..."
          rollbackTriggered = true
        }
      }

      if (!rollbackTriggered) {
        echo "Uitrollen naar ${currentEnv}..."

        sshagent(credentials: ['deploy-key']) {
          sh """
            ssh -o StrictHostKeyChecking=no jenkins@${targetHost} "cp /var/www/html/index.html /var/www/html/index.old -f"
            scp index.html jenkins@${targetHost}:/var/www/html/
          """
        }
      } else {
        // Rollback op productie
        if (currentEnv == 'main') {
          echo "Rollback naar oude versie op ${targetHost}"

          sshagent(credentials: ['deploy-key']) {
            sh """
              ssh -o StrictHostKeyChecking=no jenkins@${targetHost} "cp /var/www/html/index.old /var/www/html/index.html"
            """
          }
          currentBuild.result = 'ABORTED'
        }
      }
    }
  }
}
}
}
