pipeline {
    agent any
    environment {
        TAG = "${env.BUILD_NUMBER}"
    }
    stages {
        stage('Code Checkout') {
            agent { 
                label 'SonarQube on AWS'
            }
            steps {
                git 'https://github.com/shashank-ssriva/Employee-App-DevOps-Training.git'
            }
        }
        stage('SonarQube Analysis') {
            agent { 
                label 'SonarQube on AWS'
            }
            steps {
                withSonarQubeEnv('SonarCloud') {
                echo env.BUILD_NUMBER
                echo "${TAG}"
                sh "/usr/bin/envsubst < sonar-project.properties > /tmp/sonar-project.properties"
                sh "mv /tmp/sonar-project.properties sonar-project.properties"
                sh "cat sonar-project.properties"
                sh "/opt/sonar-scanner/bin/sonar-scanner"
            }
            }
        }

        stage('Code Build') {
            steps {
                sh "/opt/homebrew/bin/mvn clean package"
            }
        }

        stage('Publish to Nexus') {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'employees-app', classifier: '', file: 'target/employees-app-1.0.${BUILD_NUMBER}.war', type: 'war']], credentialsId: 'nexus_admin', groupId: 'com.example.employees', nexusUrl: 'http://localhost:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'war-files', version: '1.0.${BUILD_NUMBER}'
            }
        }
    }
}