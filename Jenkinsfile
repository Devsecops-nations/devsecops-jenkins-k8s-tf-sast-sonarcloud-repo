pipeline {
  agent any
  tools { 
        maven 'Maven_3_5_2'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=devsecopsguruwebapp -Dsonar.organization=devsecopsguru -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=a8cb76677dab7fb1cb7473a642e7e3b1df2c13fb'
			}
        } 
  }
}
