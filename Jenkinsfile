pipeline {
    agent any

    tools {
        maven 'Maven_3.5.2'
    }

    environment {
        SONAR_PROJECT_KEY = 'devsecopsguruwebapp'
        SONAR_ORG = 'devsecopsguruwebapp'
        SONAR_URL = 'https://sonarcloud.io'
        DOCKER_IMAGE = 'asg'
        DOCKER_REGISTRY = '593793045402.dkr.ecr.us-west-2.amazonaws.com'
        AWS_REGION = 'us-west-2'
    }

    stages {
        // Stage 1: Compile and Run Sonar Analysis
        stage('Compile and Run Sonar Analysis') {
            steps {
                echo 'Starting SonarQube Analysis...'
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    sh '''
                        mvn clean verify sonar:sonar \
                            -Dsonar.projectKey=$SONAR_PROJECT_KEY \
                            -Dsonar.organization=$SONAR_ORG \
                            -Dsonar.host.url=$SONAR_URL \
                            -Dsonar.token=$SONAR_TOKEN
                    '''
                }
            }
        }

        // Stage 2: Run Software Composition Analysis (SCA) using Snyk
        stage('Run SCA Analysis Using Snyk') {
            steps {
                echo 'Starting Snyk Analysis...'
                withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
                    sh 'mvn snyk:test -fn'
                }
            }
        }

        // Stage 3: Build Docker Image
        stage('Build') {
            steps {
                echo 'Building Docker Image...'
                script {
                    app = docker.build("$DOCKER_IMAGE")
                }
            }
        }

        // Stage 4: Push Docker Image to ECR
        stage('Push') {
            steps {
                echo 'Pushing Docker Image to Amazon ECR...'
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'AWS_CREDENTIALS']]) {
                    sh '''
                        # Authenticate Docker with ECR using AWS CLI
                        aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $DOCKER_REGISTRY

                        # Tag the Docker image
                        docker tag $DOCKER_IMAGE:latest $DOCKER_REGISTRY/$DOCKER_IMAGE:latest

                        # Push the Docker image to ECR
                        docker push $DOCKER_REGISTRY/$DOCKER_IMAGE:latest
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
        success {
            echo 'Pipeline executed successfully.'
        }
        failure {
            echo 'Pipeline execution failed.'
        }
    }
}
