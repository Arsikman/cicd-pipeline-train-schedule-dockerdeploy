pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'master'   
            }
            steps {
                script {
                    app = docker.build("karimka2302/train-schedule")
                    app.inside {
                        sh 'echo $(curl localhost:8080)'   
                    }
                }
            }  
        }
        stage('Push docker Image') {
            when {
                branch 'master'   
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('Deploy to Production') {
            when {
                branch 'master'   
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    script {
                        sh "sshpass -p '$PASSWORD' -v ssh $USERNAME@$prod_ip -o StrictHostKeyChecking=no \"docker pull karimka2302/train-schedule:latest\""
                        try {
                            sh "sshpass -p '$PASSWORD' -v ssh $USERNAME@$prod_ip -o StrictHostKeyChecking=no \"docker stop train-schedule\""
                            sh "sshpass -p '$PASSWORD' -v ssh $USERNAME@$prod_ip -o StrictHostKeyChecking=no \"docker rm train-schedule\""
                        }
                        catch (err) {
                            echo 'caught error: $err'
                        }
                        sh "sshpass -p '$PASSWORD' -v ssh $USERNAME@$prod_ip -o StrictHostKeyChecking=no \"docker run --restart always --name train-schedule -d -p 8080:8080 karimka2302/train-schedule:latest\""  
                    }
                }
            }
        }
    }
}
