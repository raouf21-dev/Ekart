pipeline {

    agent any
    tools{
        jdk 'jdk17'
        maven 'maven-3.9.9'
    }
    environment{
        SCANNER_HOME = tool "sonar-scanner"
    }
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/raouf21_dev/Ekart.git'
            }
        }
        stage('Compile') {
            steps {
                sh "mvn clean compile"
            }
        }
        
        stage('Sonarqube Analysis') {
            steps {
                sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.host.url=http://165.22.119.247:9000/ -Dsonar.login=squ_f7b4fa389629e69fa71d6d59dc2841a14980b984 -Dsonar.projectName=Ekart \
                -Dsonar.java.binaries=. \
                -Dsonar.projectKey=Ekart '''
            }
        }
        stage('OWASP SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP-check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                
            }
        }
        stage('Build Application SCAN') {
            steps {
                sh "mvn clean install"
            }
        }
        stage("Build & Push Docker Image"){
            steps{
                echo "building the docker image..."

                withCredentials([usernamePassword(credentialsId: "docker-hub-creds", passwordVariable: "PASS", usernameVariable: "USER")]){

                    sh "docker build -t santana20095/Ekart:latest f docker/Dockerfile ."
            
                    sh "echo $PASS | docker login -u $USER --password-stdin"
            
                    sh "docker push santana20095/Ekart:latest"
                }
            }
        }
    }
}