pipeline {
  agent any 
  tools {
    maven 'Maven'
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
      sshagent(['ansible']) {
      sh 'scp -o StrictHostKeyChecking=no target/*.war ansible@192.168.57.131:/home/ansible/tomcat/webapps/WebApp/webapp.war'
              }
           }
    }

  }  
}
