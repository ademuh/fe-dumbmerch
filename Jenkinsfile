def branch = "master"
def rname = "origin"
def dir = "~/fe-dumbmerch"
def credential = 'appserver'
def server = 'aimingds@103.175.219.128'
def img = 'aimingds/fe-dumbmerch'
def cont = 'frontend'

pipeline {
    agent any

    stages {
        stage('Repository Pull') {
            steps {
                sshagent([credential]){
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                    echo "Pulling Wayshub Backend Repository"
                    cd ${dir}
                    docker container stop ${cont}
                    docker container rm ${cont}
                    docker image rm ${img}:latest
                    git pull ${rname} ${branch}
                    exit
                    EOF"""
                }
            }
        }

        stage('Building Docker Image') {
            steps {
                sshagent([credential]){
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                    echo "Building Image"
                    cd ${dir}
                    docker build -t ${img}:${env.BUILD_ID} .
                    exit
                    EOF"""
                }
            }
        }

        stage('Image Deployment') {
            steps {
                sshagent([credential]){
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                    cd ${dir}
                    docker tag ${img}:${env.BUILD_ID} ${img}:latest
                    cd
                    docker-compose up -d
                    exit
                    EOF"""
                }
            }
        }

        stage('Pushing to Docker Hub (aimingds)') {
            steps {
                sshagent([credential]){
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                    cd ${dir}
                    docker image push ${img}:latest
                    exit
                    EOF"""
                }
            }
        }
        stage('Push Notification Discord') {
           steps {
                sshagent([credential]){
                    discordSend description: "dumbmerch-frontend:" + BUILD_ID, footer: "Ade Muhammad Safari - Dumbways.id Devops Batch 13", link: BUILD_URL, result: currentBuild.currentResult, scmWebUrl: '', title: 'dumbmerch-frontend', webhookURL: 'https://discordapp.com/api/webhooks/1037900028209012849/goQSYTZkg7RjV9sSIeZCxCcTldUW-rd-CpHxOZZ4qoyL6z-DoZ9ypZW1QfRcLWgnB4u8'
                }
            }
        }
    }
}
