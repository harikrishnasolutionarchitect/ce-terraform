pipeline {
  agent any
  options {
    skipDefaultCheckout(true)
  }
  stages{
    stage('clean workspace') {
      steps {
        cleanWs()
      }
    }
    stage('checkout') {
      steps {
        checkout scm
      }
    }
    
    stage('approval') {
      options {
        timeout(time: 1, unit: 'HOURS') 
      }
      steps {
        input 'approve the plan to proceed and apply'
      }
    }
    stage('tfsec') {
      agent { 
        any { image 'tfsec/tfsec-ci:v0.57.1' 
                  reuseNode true
               }
      }
      steps {
        sh '''
          tfsec . --no-color
        '''
      }
    }
    stage('SCA-checkov'){
      steps {
        sh '''
        docker run --tty --volume $(pwd):/tf bridgecrew/checkov --directory /tf --compact
        '''
      }    
    }
    
    stage('terraform') {
      steps {
        sh "echo \$(pwd)"
        sh "tfsec -f junit > tfsec_test.xml"
        sh './terraformw apply -auto-approve -no-color'
      }
    }
  }
  post {
    always {
      cleanWs()
    }
  }
}
