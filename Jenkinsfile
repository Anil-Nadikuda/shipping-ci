pipeline {
    agent {
        node {
            label 'AGENT'
        }
    }
     parameters {
        choice(name: 'DEPLOY_ENV', choices: ['blue', 'green'], description: 'Choose which environment to deploy: Blue or Green')
        choice(name: 'DOCKER_TAG', choices: ['blue', 'green'], description: 'Choose the Docker image tag for the deployment')
        booleanParam(name: 'SWITCH_TRAFFIC', defaultValue: false, description: 'Switch traffic between Blue and Green')
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

        
        stage('SonarQube Analysis') {
            steps {
                script {
                    // sh """
                    // sonar-scanner
                    // """
                    echo "Sonar-scanner"
                }
            }
        }

        stage('Trivy Analysis') {
            steps {
                script {
                    // sh """
                    // trivy fs --format table -o fs.html .
                    // """
                    echo "Trivy Scanning"
                }
            }
        }

        stage('Publish Artifact') {
            steps {
                // script {
                //     def artifactPath = "target/${env.ARTIFACT_ID}-${env.VERSION}.jar"
                    
                //     nexusArtifactUploader(
                //         nexusVersion: 'nexus3',
                //         protocol: 'http',
                //         nexusUrl: '3.89.30.99:8081',
                //         groupId: 'com.roboshop',
                //         version: "${env.VERSION}",
                //         repository: 'shipping',
                //         credentialsId: 'nexus-auth',
                //         artifacts: [
                //             [artifactId: "${env.ARTIFACT_ID}",
                //             classifier: '',
                //             file: artifactPath,
                //             type: 'jar']
                //         ]
                //     )
                // }
                echo "Nexus Artifact upload"
            }
        }
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
        ////DEPLOYMENT
        stage('Install Dependent Services') {
            steps {
                script {
                        sh "cd mongodb && helm install mongodb ."
                        sh "cd ../mysql && helm install mysql ."
                    }  
                } 
            }
        stage('Deploying Shipping Application') {
            steps {
                script {
                        sh "cd shipping && helm install shipping ."
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
