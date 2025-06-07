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


        stage('Getting fix source') {
          when       {
             branch 'fix' 
                      }
          steps {
           echo 'Getting source..'

                git branch: 'fix',
                  url: 'https://github.com/erik-aerdts/multibranch.git'
               }
        }
          stage('Getting dev source') {
          when       {
             branch 'dev' 
                      }
          steps {
           echo 'Getting source..'

                git branch: 'dev',
                  url: 'https://github.com/erik-aerdts/multibranch.git'
               }
        }
      stage('Getting main source') {
          when       {
             branch 'main' 
                      }
          steps {
           echo 'Getting source..'

                git branch: 'main',
                  url: 'https://github.com/erik-aerdts/multibranch.git'
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
stage('deploy fix') {
   when          {
      branch 'fix' }
  steps { sh """
                echo "Running Code Analysis"
                """
        }
  
                  }
  stage('deploy dev') {
   when          {
      branch 'dev' }
  steps { withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId:'jenkins', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
                sh """
                echo "Deploying Code"
                """

           script {
         
            def remote = [:];
            remote.name = "testserver";

            remote.host = "192.168.233.110";

            remote.allowAnyHosts = true;
            remote.user = USERNAME;
            remote.password = PASSWORD;
            
            sshCommand remote: remote, command: "cp /var/www/html/index.html /var/www/html/index.old -f"
            sshPut remote:remote, from: "index.html", into:'/var/www/html/'
        }
  
                  }
        }
        }
  stage('deploy test') {
   when          {
      branch 'main' }
  steps {
   withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId:'jenkins', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
                sh """
                echo "Deploying Code"
                """

           script {
         
            def remote = [:];
            remote.name = "testserver";

            remote.host = "192.168.233.111";

            remote.allowAnyHosts = true;
            remote.user = USERNAME;
            remote.password = PASSWORD;
            
            sshCommand remote: remote, command: "cp /var/www/html/index.html /var/www/html/index.old -f"
            sshPut remote:remote, from: "index.html", into:'/var/www/html/'
        }
 
                  }
  }
  }
 stage('Validate') {
   when          {
      branch 'main' }
  steps { 
          script {
            env.flagError = "false"
              try {
              input(message: 'Please validate, this job will automatically ABORTED after 30 minutes even if no user input provided', ok: 'Proceed')

                   }catch(e){
                println "input aborted or timeout expired, will try to rollback."
                env.flagError = "true"        
                             }
                  }
          }
                         }   

  stage('deploy production') {
   when          {
     allOf {
           expression { env.flagError == "false" }
      branch 'main' } }
  steps { 
             
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId:'jenkins', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
                sh """
                echo "Deploying Code"
                """


           script {
         
            def remote = [:];
            remote.name = "prodserver";

            remote.host = "192.168.233.112";

            remote.allowAnyHosts = true;
            remote.user = USERNAME;
            remote.password = PASSWORD;
            
            sshCommand remote: remote, command: "cp /var/www/html/index.html /var/www/html/index.old -f"
            sshPut remote:remote, from: "index.html", into:'/var/www/html/'
                  }   
                }
        }
                                 }

  stage("rollback if flag error true"){
        when{
           allOf {
             branch 'main'
            expression { env.flagError == "true" }
           }
        }
        steps{
          withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId:'jenkins', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
            script {
         
            def remote = [:];
            remote.name = "testserver";
            remote.host = "172.17.1.23";
            remote.allowAnyHosts = true;
            remote.user = USERNAME;
            remote.password = PASSWORD;
            
            sshCommand remote: remote, command: "cp /var/www/html/index.old /var/www/html/index.html"
            
            }       
                 }
             }
                                         }
  

}
}
