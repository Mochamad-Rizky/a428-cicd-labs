pipeline {
    agent {
        docker {
            image 'node:lts-gallium'
            args '-p 3000:3000'
        }
    }
    
    environment {
        CI = 'true'
    }
    
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        
        stage('Manual Approval') {
            steps {
                input message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed'
            }
        }
        
        stage('Deploy') {
            steps {
                sh './jenkins/scripts/deliver.sh'
                sleep 1
                sh './jenkins/scripts/kill.sh'
            }
        }
        
        stage('Deploy to EC2') {
            steps {
                sshagent(['1d660cae-20a2-41e2-b380-ee6962a8c6c3']) {
                    // join to ec2
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@ec2-54-86-109-143.compute-1.amazonaws.com'
                    sh 'ls'
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