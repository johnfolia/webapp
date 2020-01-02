pipeline {
  agent any 
  tools {
    maven 'Maven'
  }  
  environment {
       TOMCAT_CRED=credentials('deployer_user')
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
      withCredentials($TOMCAT_CRED) {
       sh 'scp -o StrictHostKeyChecking=no /var/lib/jenkins/workspace/webapp-cicd-pipeline/target/*.war ansible@192.168.57.131:/opt/tomcat/webapps/webapp.war'
              }
           }
    }

  }  
}
