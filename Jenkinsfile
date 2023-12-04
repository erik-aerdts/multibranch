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
                checkout([
                    $class: 'GitSCM', 
                    branches: [[name: '*/main']], 
                    userRemoteConfigs: [[url: 'https://github.com/erik-aerdts/multibranch.git']]
                ])
            }
        }

        stage(' Unit Testing') {
            steps {
                sh """
                echo "Running Unit Tests"
                """
            }
        }


        stage('Getting source') {
          steps {
           echo 'Getting source..'

                git branch: 'main',
                  url: 'https://github.com/erik-aerdts/multibranch.git'
               }
        }

        stage('Deploy code DEV server ') {
            steps {
                sh """
                echo "Running Code Analysis"
                """

            }
        }

       
           

       
}

}
