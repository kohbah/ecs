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
                    username: 'admin',
                    password: 'password'
                )

                rtMavenDeployer (
                    id: "maven_deployer",
                    serverId: "jfrog",
                    releaseRepo: "libs-release-local",
                    snapshotRepo: "libs-snapshot-local"
                )

                rtMavenResolver (
                    id: "maven_resolver",
                    serverId: "jfrog",
                    releaseRepo: "libs-release",
                    snapshotRepo: "libs-snapshot"
                )
            }
        }

        stage ('Exec Maven') {
            steps {
                rtMavenRun (
                    pom: 'pom.xml',
                    goals: 'clean install',
                    deployerId: "maven_deployer",
                    resolverId: "maven_resolver",
                    buildName: 'maven_app',
                    buildNumber: '0.0.1'
              )
            }
        }


       stage('Build image') {
           steps{
                script {
                  dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
              }
            }

        stage ('docker push') {
        steps {
            rtDockerPush(
                serverId: "jfrog",
                image: "http://10.0.1.113:8081/artifactory + '/sprintboot:latest'",
                // Host:
                // On OSX: 'tcp://127.0.0.1:1234'
                // On Linux can be omitted or null
                host: "tcp://127.0.0.1:1234",
                targetRepo: 'docker-local',
                // Attach custom properties to the published artifacts:
                properties: 'project-name=docker1;status=stable',
                // If the build name and build number are not set here, the current job name and number will be used:
                buildName: "my-first-build",
                buildNumber: "1"
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
