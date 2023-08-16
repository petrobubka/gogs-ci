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
                    image: alpine:3.15 // Use the appropriate Go version
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
                    sh 'sudo mkdir -p /etc/apk'
                    sh '''
                    echo -e "https://alpine.global.ssl.fastly.net/alpine/v3.18/community" > /etc/apk/repositories
                    echo -e "https://alpine.global.ssl.fastly.net/alpine/v3.18/main" >> /etc/apk/repositories
                    apk update
                    apk add --no-cache binutils go postgresql-client git openssh
                    '''
                    
                    sh 'go build -o gogs'
                    
                    sh 'go test -v -cover ./...'

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
                    sh 'sudo mkdir -p /etc/apk'
                    sh '''
                    echo -e "https://alpine.global.ssl.fastly.net/alpine/v3.18/community" > /etc/apk/repositories
                    echo -e "https://alpine.global.ssl.fastly.net/alpine/v3.18/main" >> /etc/apk/repositories
                    apk update
                    apk add --no-cache binutils go postgresql-client git openssh
                    '''
                    
                    sh 'go build -o gogs'
                    sh 'go test -v -cover ./...'

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
