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
    stage ('Build') {
      steps {
         sh 'mvn clean package'
      }
    }
    stage ('Deploy-To-Tomcat') {
     steps {
      sshagent(['ansadmin']) {
      sh 'scp -o StrictHostKeyChecking=no /opt/deployment/devsecops/*.war ansadmin@172.16.79.104:/opt/tomcat/webapps/'
              }
           }
    }
  }
}
