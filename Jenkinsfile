pipeline {
  agent any 
  tools {
    maven 'M2_HOME'
  }  
  stages {
    stage ('Initialize') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
            ''' 
      }
    }
    stage ('Check-Git-Secrets') {
      steps {
        sh 'rm /opt/devsecops-reports/trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/johnfolia/webapp.git > /opt/devsecops-reports/trufflehog'
        sh 'cat /opt/devsecops-reports/trufflehog'
      }
    }
    

    stage ('Build') {
      steps {
         sh 'mvn clean package'
      }
    }
    stage ('Deploy-To-Tomcat') {
     steps {
      sshagent(['ansadmin']) {
      sh 'scp -o StrictHostKeyChecking=no target/*.war ansadmin@172.16.79.104:/opt/tomcat/webapps/'
              }
           }
    }
  }
}


