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
      sshagent(['tomcat']) {
      sh 'cd /var/lib/jenkins/workspace/webapp-cicd-pipeline/target/' 
      sh 'scp -o StrictHostKeyChecking=no *.war root@192.168.57.131:/opt/tomcat/webapps/WebApp/WebApp.war'
              }
           }
    }

  }  
}
