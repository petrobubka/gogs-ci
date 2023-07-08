pipeline {
    agent {
        Docker { image 'node:18.16.0-alpine' }
    }

    stages {
        stage('Build') {
            when {
                // Build only pushes to the main branches
                expression { env.BRANCH_NAME ==~ /^(main|master)$/ && env.CHANGE_ID == null }
            }
            steps {
                script {
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

        stage('Pull Request') {
            when {
                // Build pull requests from all branches
                expression { env.CHANGE_ID != null }
            }
            steps {
                script {
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
}
