pipeline {
    agent {
        docker { image 'alpine:3.15' }
    }

    stages {
        stage('Build') {
            when {
                // Build only pushes to the main branches
                expression { env.BRANCH_NAME ==~ /^(main|master)$/ && env.CHANGE_ID == null }
            }
            steps {
                // Install Go inside the container
               sh '''
                    echo -e "https://alpine.global.ssl.fastly.net/alpine/v3.18/community" > /etc/apk/repositories
                    apk update
                    apk add add --no-cache binutils go 
                '''

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
                expression { env.CHANGE_ID != null }
            }
            steps {
                // Install Go inside the container
                sh 'apk add --no-cache go'

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
