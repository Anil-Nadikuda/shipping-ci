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
        SCANNER_HOME = tool 'sonar-scanner'
        
    }

    stages {
        stage('Get the version') {
            steps {
                script {
                    // Read the pom.xml and extract the artifactId and version
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
        
        stage('Build') {
            steps {
                sh 'mvn clean package'
                sh 'ls -l target'  // List the contents of the target directory
            }
        }

        //Uncomment if you want to run SonarQube analysis
        // stage('SonarQube Analysis') {
        //     steps {
        //         script {
        //             sh """
        //             sonar-scanner
        //             """
        //         }
        //     }
        // }

        // stage('Trivy Analysis') {
        //     steps {
        //         script {
        //             sh """
        //             trivy fs --format table -o fs.html .
        //             """
        //         }
        //     }
        // }

        // stage('Publish Artifact') {
        //     steps {
        //         script {
        //             def artifactPath = "target/${env.ARTIFACT_ID}-${env.VERSION}.jar"
                    
        //             nexusArtifactUploader(
        //                 nexusVersion: 'nexus3',
        //                 protocol: 'http',
        //                 nexusUrl: '3.89.30.99:8081',
        //                 groupId: 'com.roboshop',
        //                 version: "${env.VERSION}",
        //                 repository: 'shipping',
        //                 credentialsId: 'nexus-auth',
        //                 artifacts: [
        //                     [artifactId: "${env.ARTIFACT_ID}",
        //                     classifier: '',
        //                     file: artifactPath,
        //                     type: 'jar']
        //                 ]
        //             )
        //         }
        //     }
        // }
        stage('Docker image creation') {
            steps {
                script {
                    withDockerRegistry([url: 'https://index.docker.io/v1/', credentialsId: 'dockerhub_cred']) {
                        sh "docker build -t anilnadikuda/shipping:v1 ."
                        sh "docker push anilnadikuda/shipping:v1"
                    }  
                } 
            }
        }
    }
    
    post { 
        always { 
            echo 'I will always say Hello again!'
        }
        failure { 
            echo 'This runs when pipeline fails, generally used to send some alerts.'
        }
        success {
            echo 'I will say Hello when the pipeline is successful.'
        }
    }
}
