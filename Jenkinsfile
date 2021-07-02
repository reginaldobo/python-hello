def BuildTag() {
	def tag = sh script: 'git rev-parse HEAD', returnStdout:true
	return tag
	}

pipeline {
  agent any

  environment {
    SONAR_HOME = "${tool name: 'sonar-scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'}"
    BUILD_TAG = BuildTag()
    def uploadSpec =
            '''{
            "files": [
                {
                    "pattern": "*.tgz",
                    "target": "python-hello"
                }
            ]
        }'''
  } 

  stages {
/*
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
*/
    stage('Build Stage') {
      steps {
        echo '********* Build Stage Started **********'

        bat 'tar czvf python-hello-${BUILD_TAG}.tgz src/*'

        echo '********* Build Stage Finished **********'
        }
    }

    stage('Upload to Artifactory'){
      steps{
        script {
          
          echo '********* Upload Artifactory Started **********'

            def server = 'Artifactory.server "artifactory'

            server.upload(uploadSpec)
             
          echo '********* Upload Artifactory Finished **********'
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
