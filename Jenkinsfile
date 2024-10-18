pipeline {
    agent {
        node {
            label 'AGENT'
        }
    }
    tools {
        maven 'maven3'
    }
    triggers {
        // Trigger the pipeline when there's a GitHub push event
        githubPush()
    }
    stages {
        stage('Checkout Code') {
            steps {
            git branch: 'main',credentialsId: 'git-cred', url: 'https://github.com/Anil-Nadikuda/shipping-ci.git'
             sh 'ls -l'
                }
            }
            
            stage ('Build') {
                steps {
                    sh 'mvn clean package'
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
                        sonar-scanner
                        """
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