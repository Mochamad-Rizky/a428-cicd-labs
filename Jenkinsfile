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
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'AKIAXKPUZ3MI5T3WDQ42)]) {
                    sh '''
                        aws --version
                    '''
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