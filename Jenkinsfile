pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    stages {
        stage('CheckOut') {
            steps {
                git branch: 'master', credentialsId: 'GIT_HUB_REPO', url: 'https://github.com/GhofranAbdelwahab/braintuck.git'
            }
        }

        stage('Unit Test') {
            steps {
                sh "mvn clean install -U"
            }
        }

        stage('Build') {
            steps {
                sh "mvn clean package"
            }
            post {
                success {
                    archiveArtifacts 'target/*.jar'
                    sh "ls ./target"
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(installationName: 'SonarQube') {
                    sh "mvn clean verify sonar:sonar -Dsonar.login=admin -Dsonar.password=sonar -Dsonar.projectKey=BrainTuck"
                }
            }
        }

        stage('Nexus') {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'base', classifier: '', file: '/var/jenkins_home/workspace/nexus/webapp/target/webapp.war', type: 'war']], credentialsId: 'NEXUS3', groupId: 'com.braintuck', nexusUrl: '172.18.0.6:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '0.0.1-SNAPSHOT'
            }
        }
    }
}
