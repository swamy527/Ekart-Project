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
                  dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
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
        stage('docker-build') {
            steps {
                  sh 'docker build -t dockerswaha/ekart:v1 .'
            }
        }
        stage('trivy-scan') {
            steps {
                  sh 'trivy image dockerswaha/ekart:v1 > trivy-report.txt'
            }
        }
        stage('docker-publish') {
            steps {
                  withDockerRegistry(credentialsId: 'docker-cred', url: 'https://index.docker.io/v1/') {
                    sh 'docker push dockerswaha/ekart:v1'
                  }
            }
        }
        // stage('app-deploy') {
        //     steps {
        //             sh "kubectl apply -f deploymentservice.yml -n webapps"
        //         }
        // }
    }
}
