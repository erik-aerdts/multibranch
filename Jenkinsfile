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

        stage('Code Analysis') {
            steps {
                sh """
                echo "Running Code Analysis"
                """
            }
        }

        stage('Build Deploy Code') {
 

           steps {
                sh """
                echo "Building Artifact"
                """

                sh """
                echo "Deploying Code"
                """

           script {
         
            def remote = [:];
            remote.name = "testserver";
            remote.host = "172.17.1.24";
            remote.allowAnyHosts = true;
            remote.user = "jenkins";
            remote.password = "jenkins";
            
            sshCommand remote: remote, command: "cp /var/www/html/index.html /var/www/html/index.old -f"
            sshPut remote:remote, from: "index.html", into:'/var/www/html/'
            
            }
            
   
            
      }

}    


}

}
