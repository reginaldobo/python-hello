pipeline {
  agent any

  environment {
    SONAR_HOME = "${tool name: 'sonar-scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'}"
    BUILD_TAG = sh(returnStdout: true, script: "git log -n 1 --pretty=format:'%H'").trim()
  } 

  stages {

    stage('SonarQube analysis') {
      steps {
	    script {
          scannerHome = tool 'sonar-scanner'
        }
        withSonarQubeEnv('sonar-scanner') {
      	  sh "${scannerHome}/bin/sonar-scanner"
        }
      }	
    }	  

    stage('Build Stage') {
      steps {
        echo '********* Build Stage Started **********'
        sh 'tar czvf python-hello-${BUILD_TAG}.tgz src/*'
        echo '********* Build Stage Finished **********'
      }
    }

    stage('Upload to Artifactory'){
      steps{
        script {       
          echo '********* Upload Artifactory Started **********'
          def server = Artifactory.server 'artifactory'
          def uploadSpec = """{
              "files": [{
                "pattern": "*.tgz",
                "target": "example-repo-local/"
              }]
          }"""
          server.upload(uploadSpec)             
          echo '********* Upload Artifactory Finished **********'
        }
      }
    }

  }
    
  post {
    always {
        deleteDir()
      }
    success {
        echo 'Build Successfull!!'
    }
    failure {
        echo 'Sorry mate! build is Failed :('
    }  
  }

}
