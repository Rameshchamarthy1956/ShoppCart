pipeline {
    agent any
    tools{
        jdk  'java'
        maven  'maven3'
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git 'https://github.com/Chamarthy89/ShoppCart.git'
            }
        }
        
        stage('COMPILE') {
            steps {
                sh "mvn clean compile -DskipTests=true"
            }
        }
        
        stage('OWASP Scan') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Sonarqube') {
            steps {
                withSonarQubeEnv('sonar-scanner'){
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=ShoppCart \
                   -Dsonar.java.binaries=. \
                   -Dsonar.projectKey=ShoppCart '''
               }
            }
        }
        
        stage('Build') {
            steps {
                sh "mvn clean package -DskipTests=true"
            }
        }
        
        stage('Docker Build & Push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: '49367ee0-a282-4d91-bd0d-b86319442b83', toolName: 'docker') {
                        
                        sh "docker build -t shoppCart -f docker/Dockerfile ."
                        sh "docker tag  shoppCart harshateja96/shoppCart:latest"
                        sh "docker push harshateja96/shoppCart:latest"
                    }
                }
            }
        }
        
        
    }
}
