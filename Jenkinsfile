pipeline {
    agent any
    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "137.184.220.68:8081"
        NEXUS_REPOSITORY = "Grupo4Maven"
        NEXUS_CREDENTIAL_ID = "Sonar-To-Jenkins-Remote"
        
    }
    stages {
        stage('Inicio') { 
            steps {
                echo "Este es el inicio"
            }
        }
        stage('Build') { 
            steps {
                sh 'mvn -B package' 
                // sh 'mvn -B -DskipTests clean package' 
            }
        }
        stage('Test') { 
            steps {
                sh 'mvn clean verify' 
            }
        }
        stage('SonarQube analysis') {
            environment {
                SCANNER_HOME = tool 'SonarQube Scanner installer'
            }
            steps {
                withSonarQubeEnv(credentialsId: 'SonarRemote', installationName: 'SonarQube') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectKey=Grupo4 \
                    -Dsonar.projectName=Grupo4 \
                    -Dsonar.sources=src/ \
                    -Dsonar.java.binaries=target/classes/ \
                    -Dsonar.exclusions=src/test/java/****/*.java \
                    -Dsonar.projectVersion=${BUILD_NUMBER}-${GIT_COMMIT_SHORT}'''
                }
            }
        }
        stage("Publish to Local Nexus Repository Manager") {
            steps {
                script {
                    pom = readMavenPom file: "pom.xml";
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    artifactPath = filesByGlob[0].path;
                    artifactExists = fileExists artifactPath;
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: pom.version,
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]
                            ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
        }
    }
    post {
        always{
            slackSend( channel: "#grupo4", token: "ffx7Fj80ByIBWTpKm7bj3M2L", color: "good", message: "Prueba Grupo 4")
            // junit (
            //     allowEmptyResults: true,
            //     testResults: '*/test-reports/.xml'
            // )   
            //
            // slackSend( channel: "#fundamentos-de-devops", token: "slack_webhook token", color: "good", message: "${custom_msg()}")                
        }
    }

/*
   def custom_msg() {
    def JENKINS_URL= "localhost:8080"
    def JOB_NAME = env.JOB_NAME
    def BUILD_ID= env.BUILD_ID
    def JENKINS_LOG= " FAILED: Job [${env.JOB_NAME}] Logs path: ${JENKINS_URL}/job/${JOB_NAME}/${BUILD_ID}/consoleText"
    return JENKINS_LOG
   }
*/

}