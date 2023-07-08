pipeline {
    agent any

    stages {
        stage('Build') {
            when {
                // Build only pushes to the main branches
                branch 'main', 'master'
                changeset {
                    not {
                        changeRequest()
                    }
                }
            }
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

        stage('Pull Request') {
            when {
                // Build pull requests from all branches
                changeset {
                    changeRequest()
                }
            }
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
