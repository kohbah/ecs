pipeline {
    agent any
    tools {
        maven 'maven'
        jdk 'java'
        }
     stages {

        stage ('Clone') {
             steps {
                 git url: "https://github.com/kohbah/ecs.git"
            }
        }
         

       stage ('Build') {
            steps {
                sh 'mvn clean package' 
            }
       }
         
        stage ('Build docker image') {
            steps {
                script {
                    docker.build('877510168756.dkr.ecr.us-east-1.amazonaws.com/sprintboot:latest')
                }
            }
        }
         
        stage("ECR Login") {
            steps {
                withAWS(credentials:'get-login') {
                    script {
                        def login = ecrLogin()
                        sh "${login}"
                    }
                }
            }
        }
         
        stage ('publish ') {
            steps {
                script {
                     sh "docker push 877510168756.dkr.ecr.us-east-1.amazonaws.com/sprintboot:latest"
                    }        
                }
            }
        }
         
     post
        {
            always
            {
                sh "docker rmi 877510168756.dkr.ecr.us-east-1.amazonaws.com/sprintboot:latest"
            }
        }            
    }
