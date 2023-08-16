pipeline {
    agent {
        kubernetes {
            // Use the Kubernetes agent label that matches your configured Jenkins Kubernetes plugin
            label 'kubeagent'
            defaultContainer 'jnlp'
            yaml """
                apiVersion: v1
                kind: Pod
                spec:
                  containers:
                  - name: golang
                    image: golang:1.17 // Use the appropriate Go version
                    command:
                      - sleep
                      - infinity
            """
        }
    }
    
    stages {
        stage('Build') {
            when {
                expression { env.BRANCH_NAME ==~ /^(main|master)$/ && env.CHANGE_ID == null }
            }
            steps {
                container('golang') {
                    // Install Go inside the container
                    sh 'ls'
                    checkout scm
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
                expression { env.CHANGE_ID != null }
            }
            steps {
                container('golang') {

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
