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
         
        stage ('Build docker image') {
            steps {
                script {
                    shouldPublish = input message: 'Publish Containers?', parameters: [[$class: 'ChoiceParameterDefinition', choices: 'yes\nno', description: '', name: 'Deploy']]
                    if(shouldPublish == "yes") {
                     echo "Publishing docker containers"
                     sh "\$(aws ecr get-login)"

                     sh "docker tag sprintboot:latest 877510168756.dkr.ecr.us-east-1.amazonaws.com/sprintboot:latest"
                     sh "docker push 877510168756.dkr.ecr.us-east-1.amazonaws.com/sprintboot:latest"
                }
            }
        }
}
