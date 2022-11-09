pipeline
{
  // * Project plugins:
  // * SonarQube Scanner
 
  agent { label 'agent2!!!' }
  environment { 
    ARTIF_TOKEN = credentials('Calculator_artifactory') 
  }
  tools {
    maven 'maven 3.8.6'
  }

  stages {
    stage('build && SonarQube analysis') {
        steps {
            withSonarQubeEnv(installationName: 'sonar_qube') {
                sh 'mvn clean package sonar:sonar'
            }
        }
    }

   stage("Quality gate") {
          steps {
                timeout(time: 3, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true
                        }
          }
       }

    stage('Deliver to artifactory') {
        steps {
		sh 'jf rt upload --url http://172.31.65.100:8082/artifactory/calculator --access-token ${ARTIF_TOKEN} target/Calculator-1.0-SNAPSHOT.jar calculator_app/'  
        }
    }
  }
   post {
            success {
		mail bcc: '', body: "Pipeline ${currentBuild.fullDisplayName} succeeded", cc: '', from: '', replyTo: '', subject: 'Pipeline status', to: 'doronamsalem100@yahoo.com'
	    }

            failure {
		mail bcc: '', body: "Pipeline ${currentBuild.fullDisplayName} FAILED", cc: '', from: '', replyTo: '', subject: 'Pipeline status', to: 'doronamsalem100@yahoo.com'
            }
    }
}
