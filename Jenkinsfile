pipeline{
    agent any
    environment {
        VERSION = "${env.BUILD.ID}"
    
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
        stage('docker build & push to nexus repo'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'nexus-cred', variable: 'nexus-cred')])
                        sh 'docker build -t 18.214.99.97:8083/springapp:${VERSION} .'
                        sh 'docker login -u admin -p $nexus-cred 18.214.99.97:8083'
                        sh 'docker push 18.214.99.97:8083/springapp:${VERSION}'
                        sh 'docker rmi 18.214.99.97:8083/springapp:${VERSION}'
                    }
                }
            }
        }
        
}
