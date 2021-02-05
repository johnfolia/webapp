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
        sshagent(['ansadmin']) {
        sh 'ssh -o StrictHostKeyChecking=no ansadmin@172.16.79.122'
        sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/johnfolia/webapp.git > trufflehog'
        sh 'cat trufflehog'
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
