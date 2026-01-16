pipeline {
    agent any
    
    tools {
        maven 'maven3.9'
        jdk 'JDK21'
    }
    
    environment {
        SONAR_HOST_URL = 'http://localhost:9000'
        NEXUS_URL = 'http://172.31.20.130:8081/repository/userportal/'
        TOMCAT_URL = 'http://localhost:8080'
        TOMCAT_WEBAPPS = '/opt/tomcat/webapps'
        WAR_FILE = 'devops-userportal.war'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Tmlehin-dev/userportal'
            }
        }
        
        stage('Maven Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        
       /* stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
        
        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: false
                }
            }
        }
        
        stage('Deploy to Tomcat') {
            steps {
                script {
                    sh """
                        sudo rm -rf ${TOMCAT_WEBAPPS}/*
                        sudo cp target/${WAR_FILE} ${TOMCAT_WEBAPPS}/ROOT.war
                        sudo systemctl restart tomcat
                    """
                }
            }
        }
        
        stage('Deploy to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus-credentials', 
                                                  usernameVariable: 'NEXUS_USER', 
                                                  passwordVariable: 'NEXUS_PASS')]) {
                    sh """
                        mvn deploy:deploy-file \
                            -DgroupId=com.userportal \
                            -DartifactId=devops-userportal \
                            -Dversion=1.0.0-${BUILD_NUMBER} \
                            -Dpackaging=war \
                            -Dfile=target/${WAR_FILE} \
                            -DrepositoryId=nexus \
                            -Durl=${NEXUS_URL} \
                            -Dusername=${NEXUS_USER} \
                            -Dpassword=${NEXUS_PASS}
                    """
                }
            }
        }*/
    } 
    
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
        always {
            cleanWs()
        }
    }
}

