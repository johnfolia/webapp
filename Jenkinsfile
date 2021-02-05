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
}
