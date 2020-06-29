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
       stage ('Add properties') {
            // Attach custom properties to the published artifacts:
            rtDocker.addProperty("project-name", "docker1").addProperty("status", "stable")
        }

       stage ('Build docker image') {
            docker.build(ARTIFACTORY_DOCKER_REGISTRY + '/sprint:latest', .)
        }

       stage ('Push image to Artifactory') {
            buildInfo = rtDocker.push ARTIFACTORY_DOCKER_REGISTRY + '/spring:latest', 'docker-local'
        }

       stage ('Publish build info') {
            server.publishBuildInfo buildInfo
        }
     }
}
