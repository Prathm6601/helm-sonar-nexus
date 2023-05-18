pipeline{
    agent any
    
    stages {
        
        stage('Git Checkout'){
            
            steps{
                
                script{
                    
                    git branch: 'main', url: 'https://github.com/Prathm6601/helm-sonar-nexus.git'
                }
            }
        }
        stage('UNIT testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn test'
                }
            }
        }
        stage('Integration testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }
        stage('Maven build'){
            
            steps{
                
                script{
                    
                    sh 'mvn clean install'
                }
            }
        }
        stage('Static code analysis'){
            
            steps{
                
                script{
                    
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                        
                        sh 'mvn clean package sonar:sonar'
                    }
                }
            }
        }
        stage('Quality Gate Status'){
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage('docker build and push image to the nexus repo'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'nexus-cred', variable: 'nexus-cred')]){
                        sh 'docker image build -t 35.175.142.195:8083/$JOB_NAME:v1.$BUILD_ID .'
                        sh 'docker login -u admin -p prathm 35.175.142.195:8083'
                        sh 'docker image push 35.175.142.195:8083/$JOB_NAME:v1.$BUILD_ID'
                        
                    }
                }
            }
        }
        
    }
        
}
