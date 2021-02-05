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
  }  
     
    stage ('Build') {
      steps {
         sh 'mvn clean package'
      }
    }
   
   stage ('Deploy-To-Tomcat') {
     steps {
      sshagent (credentials: ['ansadmin']) {
                ansiblePlaybook(
                    credentialsId: 'ansadmin',
                    inventory: 'hosts',
                    installation: 'Ansible-Software',
                    limit: '172.16.79.126',
                    playbook: '/opt/playbooks/DevSecOps/webapp/deploy-war.yml',
                    extras: 'options and var for instance verbose mode : -vvv'
                )   
            }
           }
    }
}
