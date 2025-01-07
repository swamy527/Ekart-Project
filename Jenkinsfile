pipeline {
    agent any
    tools{
        maven 'maven3'
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('unit test') {
            steps {
                sh 'mvn test -Dmaven.test.skip=true'
            }
        }
        stage('sonarqube-analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh '$SCANNER_HOME/bin/sonar-scanner -Dsonar.java.binaries=. -Dsonar.projecKey=ekart -Dsonar.projectName=ekart'
                 }
            }
        }
    }
}
