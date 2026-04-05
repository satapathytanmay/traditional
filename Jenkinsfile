pipeline {
    agent any

    tools {
        maven 'Maven3'   // configure in Jenkins
        jdk 'Java8'      // configure in Jenkins
    }

    environment {
        NEXUS_URL = "http://<NEXUS-IP>:8081"
        NEXUS_REPO = "maven-snapshots"
        GROUP_ID = "com.example"
        ARTIFACT_ID = "traditional-app"
        VERSION = "1.0-SNAPSHOT"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/satapathytanmay/traditional.git'
            }
        }

        stage('Build WAR') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Upload to Nexus') {
            steps {
                sh """
                curl -v -u admin:admin123 \
                --upload-file target/traditional-app.war \
                ${NEXUS_URL}/repository/${NEXUS_REPO}/com/example/traditional-app/1.0-SNAPSHOT/traditional-app-1.0-SNAPSHOT.war
                """
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                sshagent(['tomcat-server-ssh']) {
                    sh """
                    scp target/traditional-app.war ec2-user@<TOMCAT-IP>:/opt/tomcat/webapps/
                    ssh ec2-user@<TOMCAT-IP> 'cd /opt/tomcat/bin && ./shutdown.sh && ./startup.sh'
                    """
                }
            }
        }
    }
}
