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
            }
        }
    } catch (e) {
        // Handle any errors
        currentBuild.result = 'FAILED'
        throw e
    }
}