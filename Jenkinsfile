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
                    def shippingId = pom.artifactId
                    echo "application version: ${env.SHIPPING_ID}"
                }
            }
        }
            
            stage ('Build') {
                steps {
                    sh 'mvn clean package'
                    sh 'ls -l'
                }
            }

            stage('Test') {
                steps {
                    sh 'mvn test'
                }
            }

            stage('SonarQube Analysis') {
                steps {
                    sh """
                        sonar-scanner -DskipTests=true
                        """
                }
            }

            stage('Nexus Artifact Upload') {
                steps {
                        nexusArtifactUploader(
                            nexusVersion: 'nexus3',
                            protocol: 'http',
                            nexusUrl: 'http://3.89.30.99:8081/repository/maven-releases/',
                            groupId: 'com.roboshop',
                            version: "${env.SHIPPING_ID}",
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