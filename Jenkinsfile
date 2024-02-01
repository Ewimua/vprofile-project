pipeline {
    agent any
    tools {
        maven "MAVEN3"
        jdk "jdk8"
    }

    environment {
      SNAP_REPO = 'vprofile-snapshot' 
      NEXUS_USER = 'admin'
      NEXUS_PASS ='school1@12'
      RELEASE_REPO = 'vprofile-release' 
      CENTRAL_REPO = 'vprofle-mavan-central'
      NEXUS_IP = '192.168.1.85'
      NEXUS_PORT = '8081'
      NEXUS_GRP_REPO = 'vprofile-maven-group'
      NEXUS_LOGIN = 'nexuslogin'
      SONARSERVER = 'sonarserver'
      SONARSCANNER = 'sonarscanner'
    }

    stages {
        stage("Build"){
            steps {
                sh 'mvn -s settings.xml -DskipTests install'
            }

            post {
                success {
                    echo "Now Archiving."
                    archiveArtifacts artifacts: '**/*.war'
                }  
            }
        }
        stage("Test"){
            steps {
                sh 'mvn  -s settings.xml test'
            }
        }

        stage ('Checkstyle Analysis'){
            steps {
                sh 'mvn -s settings.xml checkstyle:checkstyle'
            }
        }

        stage('Sonar Analysis'){
            environment {
                scannerHome = tool "${SONARSCANNER}"
            }
            steps {
                withSonarQubeEnv("${SONARSERVER}") {
                    sh '''${scannerHome}/bin/sonar-scanner -Dsonar.ProjectKey=vprofile \
                    -Dsonar.projectName=vprofile \
                    -Dsonar.projectVersion=1.0 \
                    -Dsonar.sources=src/ \
                    -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                    -Dsonar.junit.reportsPath=target/surefire-reports/ \
                    -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                    -Dsonar.java.checksytle.reportsPath=target/checkstyle-results.xml'''
                }
            }
        }
    }
}