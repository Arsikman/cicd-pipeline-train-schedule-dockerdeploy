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
                    docker.build("karimka2303/train-schedule")
                    Image.inside[("karimka2303/train-schedule")] {
                        sh 'curl locahost:8080'   
                    }
                }
            }   
        }
    }
}
