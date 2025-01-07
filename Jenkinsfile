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
                    sh '$SCANNER_HOME/bin/sonar-scanner -Dsonar.java.binaries=. -Dsonar.projectKey=ekart -Dsonar.projectName=ekart'
                 }
            }
        }
        stage('dependency-check') {
            steps {
                  dependencyCheck additionalArguments: ' --scan ./', odcInstallation: 'depend-check'
                  dependencyCheckPublisher pattern: '**/dependency-check.xml'
            }
        }
        stage('build') {
            steps {
                  sh 'mvn package -Dmaven.test.skip=true'
            }
        }
        stage('publish') {
            steps {
                  withMaven(globalMavenSettingsConfig: 'global-maven', jdk: '', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                      sh 'mvn deploy -Dmaven.test.skip=true'
                  }
            }
        }
    }
}
