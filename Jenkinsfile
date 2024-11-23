node {
    def dockerImage
    
    try {
        stage('Setup') {
            // Configure Docker agent
            dockerImage = docker.image('node:lts-buster-slim')
            dockerImage.pull()
            
            dockerImage.inside('-p 3000:3000') {
                // Set environment variable
                env.CI = 'true'
                
                stage('Build') {
                    sh 'npm install'
                }
                
                stage('Test') {
                    sh './jenkins/scripts/test.sh'
                }
                
                stage('Manual Approval') {
                    input message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed'
                }
                
                stage('Deploy') {
                    sh './jenkins/scripts/deliver.sh'
                    sleep time: 1, unit: 'MINUTES'
                    sh './jenkins/scripts/kill.sh'
                }
                stage('Deploy to EC2') {
                    def ec2_ip = 'ec2-54-86-109-143.compute-1.amazonaws.com'
                    def ec2_user = 'ubuntu'
                    def ec2_key = './submission-ci-cd.pem'
                    
                    // SSH to EC2 and deploy
                    sh """
                    ssh -i ${ec2_key} ${ec2_user}@${ec2_ip} 
                    mkdir -p /home/ubuntu/app && cd /home/ubuntu/app
                    git clone https://github.com/Mochamad-Rizky/a428-cicd-labs -b react-app && cd react-app
                    npm install
                    npm start
                    """
                }
            }
        }
    } catch (e) {
        currentBuild.result = 'FAILED'
        throw e
    }
}