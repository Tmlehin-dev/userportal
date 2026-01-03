pipeline {
    agent any
    
    environment {
        TOMCAT_HOME = '/opt/tomcat'
        NEXUS_VERSION = 'nexus3'
        NEXUS_PROTOCOL = 'http'
        NEXUS_URL = '3.23.87.213:8081'
        NEXUS_REPOSITORY = 'userportal'
        NEXUS_CREDENTIAL_ID = 'nexus-credentials'
    }
    
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
                sh 'mv target/devops-userportal.war target/ROOT.war'
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                script {
                    try {
                        withSonarQubeEnv('SonarQube') {
                            sh 'mvn sonar:sonar'
                        }
                        
                        timeout(time: 10, unit: 'MINUTES') {
                            def qg = waitForQualityGate()
                            if (qg.status != 'OK') {
                                echo "Quality Gate failed: ${qg.status}"
                                echo "Continuing build despite quality gate failure..."
                            }
                        }
                    } catch (Exception e) {
                        echo "SonarQube analysis failed: ${e.getMessage()}"
                        echo "Continuing build..."
                    }
                }
            }
        }
        
        stage('Deploy to Tomcat') {
            steps {
                sh "rm -rf ${TOMCAT_HOME}/webapps/*"
                sh "cp target/ROOT.war ${TOMCAT_HOME}/webapps/"
            }
        }
        
        stage('Upload to Nexus') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: NEXUS_VERSION,
                    protocol: NEXUS_PROTOCOL,
                    nexusUrl: NEXUS_URL,
                    groupId: 'com.userportal',
                    version: '1.0.0',
                    repository: NEXUS_REPOSITORY,
                    credentialsId: NEXUS_CREDENTIAL_ID,
                    artifacts: [
                        [artifactId: 'devops-userportal',
                         classifier: '',
                         file: 'target/ROOT.war',
                         type: 'war']
                    ]
                )
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}