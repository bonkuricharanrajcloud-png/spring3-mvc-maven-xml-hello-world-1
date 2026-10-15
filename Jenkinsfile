pipeline {
    agent any 
    tools {
        maven "MVN_HOME"
    }
    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "3.90.78.242:8081"
        NEXUS_REPOSITORY = "devops"
        NEXUS_CREDENTIAL_ID = "Nexus_server"
    }
    stages {
        stage("clone code") {
            steps {
                script {
                    git 'https://github.com/bonkuricharanrajcloud-png/spring3-mvc-maven-xml-hello-world-1.git'
                }
            }
        }
        stage("mvn build") {
            steps {
                script {
                    sh 'mvn -Dmaven.test.failure.ignore=true install'
                }
            }
        }
        stage("publish to nexus") {
            steps {
                script {
                    // Extract properties directly using standard read file/regex or hardcode/fallback variables
                    def groupId = "com.ncodeit"
                    def artifactId = "ncodeit-hello-world"
                    def packaging = "war"
                    
                    def artifactPath = "target/${artifactId}-${BUILD_NUMBER}.${packaging}"
                    // Alternatively find the war file dynamically:
                    def files = findFiles(glob: "target/*.war")
                    if (files.length > 0) {
                        artifactPath = files[0].path
                    }

                    if (fileExists(artifactPath)) {
                        echo "*** File: ${artifactPath}, group: ${groupId}, packaging: ${packaging}, version ${BUILD_NUMBER}"
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: groupId,
                            version: "${BUILD_NUMBER}",
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                [artifactId: artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: packaging],
                                [artifactId: artifactId,
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
}
