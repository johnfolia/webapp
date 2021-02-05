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
        sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/johnfolia/webapp.git > trufflehog'
        sh 'cat trufflehog'
      }
    }

    stage ('Source Composition Analysis') {
      steps {
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/johnfolia/webapp/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'
         sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
        
      }
    }
 

   stage ('SAST') {
      steps {
        withSonarQubeEnv('sonar') {
          sh 'mvn sonar:sonar'
          sh 'cat target/sonar/report-task.txt'
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
                    extras: ' options and var that you want add for instance verbose mode : -vvv'
 )
            }
           }
    }

   stage ('DAST') {
      steps {
        sshagent(['dockeradmin']) {
         sh 'ssh -o  StrictHostKeyChecking=no dockeradmin@192.168.57.132 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://192.168.57.131:8080/WebApp/" || true'
        }
      }
    }


  }  
}
