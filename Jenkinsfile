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
      sshagent(['ansadmin']) {
      sh 'scp -o StrictHostKeyChecking=no target/*.war ansadmin@172.16.79.104:/opt/tomcat/webapps/'
              }
           }
    }

    stage ('DAST') {
      steps {
        sshagent(['ansadmin']) {
         sh 'ssh -o  StrictHostKeyChecking=no ansadmin@172.16.79.105 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://172.16.79.104:8080/WebApp/" || true'
        }
      }
    }

   
  }
}


