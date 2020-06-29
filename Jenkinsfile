pipeline {
    agent any
    tools {
        maven 'maven'
        jdk 'java'
    }

    stages {

        stage('build && SonarQube analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                        sh 'mvn clean package sonar:sonar'
                }
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
        stage ("publish to artifactory") {
            steps {
                timeout(time: 2, unit: "MINUTES") {
                    input message: 'do you want to publish this build to artifactory server?', ok: 'Yes'
             }
            }
        }
        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: "jfrog",
                    buildName: 'maven_app',
                    buildNumber: '0.0.1'
                )
            }
        }
        stage ('Deploy') {
            steps{
              sshPublisher(publishers: [sshPublisherDesc(configName: 'application', sshCredentials: [encryptedPassphrase: '{AQAAABAAAAAQ7DGcMX9bver+mzFryZxeLC8Tx82oB/olkQbqqg4bwYQ=}', key: '', keyPath: '', username: 'jenkins'], transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'java -jar /home/jenkins/app01/hello/my-app-1.0-SNAPSHOT.jar', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '/app01/hello', remoteDirectorySDF: false, removePrefix: 'target', sourceFiles: '**/*.jar')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])            }
        }
    }
    post {
        always {
             echo 'deleting the current directory'
             deleteDir()
             echo 'deleting @tmp directory'
             dir("${workspace}@tmp") {
                 deleteDir()
                 }
            }
    }
