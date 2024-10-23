pipeline {
    agent {
        node {
            label 'AGENT'
        }
    }
    tools {
        maven 'maven3'
    }
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Get the version') {
            steps {
                script {
                    def pom = readMavenPom file: 'pom.xml'
                    def artifactId = pom.artifactId
                    def version = pom.version
                    
                    // Print the extracted artifactId and version
                    echo "Artifact ID: ${artifactId}"
                    echo "Version: ${version}"

                    // Use these values in further stages if needed
                    env.ARTIFACT_ID = artifactId
                    env.VERSION = version
                }
            }
        }
            
            stage ('Build') {
                steps {
                    sh 'mvn clean package'
                    sh 'ls -l'
                }
            }

            // stage('SonarQube Analysis') {
            //     steps {
            //         script{
            //             sh """
            //             sonar-scanner 
            //             """
            //         }
            //     }
            // }

            stage ('Nexus Artifact Upload') {
                steps {
                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: 'http://3.89.30.99:8081/repository/maven-releases/',
                        groupId: 'com.roboshop',
                        version: "${env.VERSION}",
                        repository: 'maven-releases',
                        credentialsId: 'nexus-auth',
                        artifacts: [
                            [artifactId: shipping,
                            classifier: '',
                            file: 'shipping' + version + '.jar',
                            type: 'jar']
                        ]
                        )
                    }
                }
            }
    
        post { 
            always { 
                echo 'I will always say Hello again!'
                deleteDir()
            }
            failure { 
                echo 'this runs when pipeline is failed, used generally to send some alerts'
            }
            success{
                echo 'I will say Hello when pipeline is success'
            }
    }
    
}