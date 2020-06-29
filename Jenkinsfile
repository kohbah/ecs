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
         
        stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: "jfrog",
                    url: 'http://10.0.1.113:8081/artifactory',
                    username: 'admin',
                    password: 'APAqMjUbMocmeiNBRhzi49byFi8'
                )
            }
        }
         
        stage ('Build docker image') {
            steps {
                script {
                    docker.build('10.0.1.113:8081/dockerrepo/hello-world:latest')
                }
            }
        }

        stage ('Push image to Artifactory') {
            steps {
                rtDockerPush(
                    serverId: "jfrog",
                    image: '10.0.1.113:8081/dockerrepo/hello-world:latest',
                    targetRepo: 'dockerrepo',
                    // Attach custom properties to the published artifacts:
                    properties: 'project-name=docker1;status=stable'
                )
            }
        }

        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: "jfrog"
                )
            }
        }
     }
}
