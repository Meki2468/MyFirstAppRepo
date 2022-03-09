pipeline {
    agent any
    
    tools {
        maven 'Maven3'
    }
    
    stages {
        
        stage ('checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: '9a533c9f-9052-48a9-bef0-1f46343fdb89', url: 'https://github.com/Meki2468/MyFirstAppRepo']]])
            }
        }
    
        stage ('Build') {
            steps {
                sh 'mvn clean install -f MyWebApp/pom.xml'
  
                }
            }
            
        stage ('Code Quality upload') {
            steps {
                withSonarQubeEnv('sonarQube') {
                sh 'mvn sonar:sonar -f MyWebApp/pom.xml'
                    }
                }
            }
            
        stage ('nexus upload') {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'MyWebApp', classifier: '', file: 'MyWebApp/target/MyWebApp.war', type: 'war']], credentialsId: '9a1442b4-6611-4430-b4c5-3269158084db', groupId: 'com.dept.app', nexusUrl: 'ec2-3-83-116-110.compute-1.amazonaws.com:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0-SNAPSHOT'
                }    
            }
            
        stage ('Dev Deploy') {
            steps {
                deploy adapters: [tomcat8(credentialsId: '79189b85-79b2-4699-9b05-55ac54321fbd', path: '', url: 'http://ec2-44-198-186-7.compute-1.amazonaws.com:8090')], contextPath: null, war: '**/*.war'
                }
            }
            
        stage ('Slack Notify') {
            steps {
                slackSend channel: 'devops-channel01', message: 'Dev Deployment was successful', tokenCredentialId: '0df37b20-feaa-40d1-93a1-d4d30a6427c2'
                }
            }
            
        stage ('Dev Approval') {
            steps {
                echo "Taking approval from DEV Manager for QA Deployment"
                timeout(time: 7, unit: 'DAYS') {
                input message: 'Do you want to deploy into QA?', submitter: 'admin'
                }
            }
        }
        
        stage ('QA Deploy') {
            steps {
                deploy adapters: [tomcat8(credentialsId: '79189b85-79b2-4699-9b05-55ac54321fbd', path: '', url: 'http://ec2-44-198-186-7.compute-1.amazonaws.com:8090')], contextPath: null, war: '**/*.war'
                }
            }
            
        stage ('Slack Notify QA') {
            steps {
                slackSend channel: 'devops-channel01', message: 'QA Deployment was successful pleas start your testing', tokenCredentialId: '0df37b20-feaa-40d1-93a1-d4d30a6427c2'
                }
            }
        }
    }
    
    
    
    
