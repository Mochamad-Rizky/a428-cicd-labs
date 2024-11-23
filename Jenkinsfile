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
                sleep time: 1, unit: 'SECOND'
                sh './jenkins/scripts/kill.sh'
            }
        }
        
        stage('Deploy to EC2') {
            steps {
                script {
                    def ec2_ip = 'ec2-54-86-109-143.compute-1.amazonaws.com'
                    def ec2_user = 'ubuntu'
                    def ec2_key = './submission-ci-cd.pem'
                
                    sh """
                    ssh -i ${ec2_user}@${ec2_ip}
                    mkdir -p /home/ubuntu/app && cd /home/ubuntu/app
                    git clone https://github.com/Mochamad-Rizky/a428-cicd-labs -b react-app && cd react-app
                    npm install
                    npm start
                    EOF
                    """
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