pipeline {
    agent any

    // Tools configuration
    tools { 
        maven 'Maven_3.5.2'  // Use the specified Maven version
    }

    environment {
        SONAR_PROJECT_KEY = 'devsecopsguruwebapp'
        SONAR_ORGANIZATION = 'devsecopsguruwebapp'
        SONAR_HOST_URL = 'https://sonarcloud.io'
        SNYK_TOKEN_ID = 'SNYK_TOKEN' // ID for Snyk credentials
    }

    stages {

        stage('Compile and Run Sonar Analysis') {
            steps {
                echo 'Running SonarQube Analysis...'
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    sh '''
                        mvn clean verify sonar:sonar \
                        -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                        -Dsonar.organization=${SONAR_ORGANIZATION} \
                        -Dsonar.host.url=${SONAR_HOST_URL} \
                        -Dsonar.login=${SONAR_TOKEN}
                    '''
                }
            }
        }

        stage('Run SCA Analysis Using Snyk') {
            steps {
                echo 'Running Snyk Security Analysis...'
                withCredentials([string(credentialsId: SNYK_TOKEN_ID, variable: 'SNYK_TOKEN')]) {
                    sh '''
                        mvn snyk:test -fn \
                        -Dsnyk.token=${SNYK_TOKEN}
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace...'
            cleanWs() // Clean workspace after build completion
        }
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline execution failed. Check the logs for details.'
        }
    }
}
