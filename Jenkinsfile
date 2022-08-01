pipeline {
    agent any

    stages {
        stage('Git checkout') {
            steps {
                git 'https://github.com/Pilas01/realsonar-repo.git'
            }
        }
        
        stage('Build with maven') {
            steps {
                sh 'cd SampleWebApp && mvn clean install'
            }
        }
        
             stage('Test') {
            steps {
                sh 'cd SampleWebApp && mvn test'
            }
        
            }
        stage('Code Qualty Scan') {

           steps {
                  withSonarQubeEnv('sonarserver') {
             sh "mvn -f SampleWebApp/pom.xml sonar:sonar"      
               }
            }
       }
        stage('Quality Gate') {
           steps {
                 waitForQualityGate abortPipeline: true
           }
        }

        stage('push to Nexus') {
          steps {    
            nexusArtifactUploader artifacts: [[artifactId: 'SampleWebApp', classifier: '', file: 'SampleWebApp/target/SampleWebApp.war', type: 'war']], credentialsId: 'nexus', groupId: 'SampleWebApp', nexusUrl: '3.90.111.199:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0-SNAPSHOT'
          }

        }
        
        stage('Deploy to tomcat') {
           steps {
               deploy adapters: [tomcat9(credentialsId: 'dbf80444-e2f0-4535-99f6-2e36d8296b02', path: '', url: 'http://54.89.190.107:8080/')], contextPath: 'myapp', war: '**/*.war'
            }
        }
    }
}
