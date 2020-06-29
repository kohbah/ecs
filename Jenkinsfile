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
        stage ('Build docker image') {
            steps {
                script {
                    docker.build(ARTIFACTORY_DOCKER_REGISTRY + '/hello-world:latest')
                }
            }
        }

        stage ('Push image to Artifactory') {
            steps {
                rtDockerPush(
                    serverId: "jfrog",
                    image: ARTIFACTORY_DOCKER_REGISTRY + '/hello-world:latest',
                    targetRepo: 'docker-local',
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
