pipeline {
    agent any
    tools {
        maven 'maven'
        jdk 'java'
        }
     stages {
        stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: "jfrog",
                    url: 'http://10.0.1.113:8081/artifactory',
                    credentialsId: 'jfrog'
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

                  sh "docker build -t spring-boot-websocket-chat-demo ."
             }
        }

        stage ('docker push') {
        steps {
            rtDockerPush(
                serverId: "jfrog",
                image: "http://10.0.1.113:8081/docker-local/spring-boot-websocket-chat-demo",
                host: 'tcp://127.0.0.1:1234'
                // On Linux can be omitted or null
                targetRepo: 'docker-repo',
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
