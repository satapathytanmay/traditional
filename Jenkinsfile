pipeline {
    agent any

    tools {
        maven 'M2_HOME'   // configure in Jenkins
        jdk 'JAVA_HOME'      // configure in Jenkins
    }

    environment {
        NEXUS_URL = "http://16.170.234.219:8081"
        NEXUS_REPO = "maven-snapshot"
        GROUP_ID = "com.example"
        ARTIFACT_ID = "traditional-app"
        VERSION = "1.0-SNAPSHOT"
    }

    stages {
        stage('Build WAR') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Upload to Nexus') {
            steps {
                sh """
                curl -v -u admin:admin \
                --upload-file target/traditional-app.war \
                ${NEXUS_URL}/repository/${NEXUS_REPO}/com/example/${ARTIFACT_ID}/${VERSION}/${ARTICAT_ID}-${VERSION}.war
                """
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                    sh """
                    cp target/traditional-app.war /home/ec2-user/tomcat10/webapps/
                    cd /home/ec2-user/tomcat10/bin  
                    ./shutdown.sh 
                    ./startup.sh
                    """
                }
            }
        
    }
}
