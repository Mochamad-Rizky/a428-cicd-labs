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
            }
        }
    } catch (e) {
        currentBuild.result = 'FAILED'
        throw e
    }
}