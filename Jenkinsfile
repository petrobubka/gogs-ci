pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                // Clean up any previous build artifacts
                sh 'rm -f gogs'

                // Build the project using go build
                sh 'go build -o gogs'

                // Verify if the build was successful
                script {
                    if (fileExists('gogs')) {
                        echo 'Build successful'
                    } else {
                        error 'Build failed'
                    }
                }
            }
        }
    }
}
