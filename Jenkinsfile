pipeline {
    agent { label 'ec2-agent' }
    stages {
        stage('Setup') {
            steps {
                script {
                    dockerImage = docker.image('node:lts-buster-slim')
                    dockerImage.pull()
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    dockerImage.inside('-p 3000:3000') {
                        env.CI = 'true'
                        sh 'npm install'
                    }
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    dockerImage.inside('-p 3000:3000') {
                        sh './jenkins/scripts/test.sh'
                    }
                }
            }
        }
        stage('Manual Approval') {
            steps {
                input message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed'
            }
        }
        stage('Deploy') {
            steps {
                script {
                    dockerImage.inside('-p 3000:3000') {
                        sh './jenkins/scripts/deliver.sh'
                        sleep time: 1, unit: 'MINUTES'
                        sh './jenkins/scripts/kill.sh'
                    }
                }
            }
        }
    }
    post {
        failure {
            script {
                currentBuild.result = 'FAILED'
            }
        }
    }
}