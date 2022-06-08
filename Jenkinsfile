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
            steps {
                script {
                    app = docker.build("karimka2303/train-schedule")
                    app.inside {
                        sh 'curl locahost:8080'   
                    }
                }
            }   
        }
    }
}
