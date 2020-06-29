pipeline {
    agent any
    tools {
        maven 'maven'
        jdk 'java'
        }
     stages {

        stage ('Clone') {
             steps {
                 git branch: 'master', url: "https://github.com/kohbah/ecs.git"
            }
        }
         
        stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: "jfrog",
                    url: 'http://10.0.1.113:8081/artifactory',
                    username: 'admin',
                    password: 'password'
                )
            }
        }

       stage ('Build') {
            steps {
                sh 'mvn clean package' 
            }
       }
       stage("Docker build") {
             steps {

                  sh "docker build -t springboot:latest ."
             }
        }
        
        stage ('docker push') {
            steps {
                rtDockerPush(
                    serverId: "jfrog",
                    image: "http://10.0.1.113:8081/artifactory + '/springboot:latest'"
                    targetRepo: 'docker-local',
                    // Attach custom properties to the published artifacts:
                    properties: 'project-name=docker1;status=stable'
                 )

                }
             }

         stage ('publish docker image') {
            steps {
                rtPublishBuildInfo (
                    serverId: "jfrog"
                )
         }
     }
 }
}
