pipeline {
    agent {
        kubernetes {
            // Use the Kubernetes agent label that matches your configured Jenkins Kubernetes plugin
            label 'kubeagent'
            yaml """
                apiVersion: v1
                kind: Pod
                spec:
                  containers:
                  - name: alpine
                    image: alpine:3.15
                    command:
                    - sleep
                    args:
                    - 99d
            """
        }
    }
    
    stages {
        stage('Build') {
            when {
                expression { env.BRANCH_NAME ==~ /^(main|master)$/ && env.CHANGE_ID == null }
            }
            steps {
                container('alpine') {
                    sh '''
                    echo -e "https://alpine.global.ssl.fastly.net/alpine/v3.18/community" > /etc/apk/repositories
                    echo -e "https://alpine.global.ssl.fastly.net/alpine/v3.18/main" >> /etc/apk/repositories
                    apk update
                    apk add --no-cache binutils go postgresql-client git openssh
                    '''
                    sh 'git config --global --add safe.directory .'
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

        stage('Another Stage') {
            when {
                expression { env.CHANGE_ID != null }
            }
            steps {
                container('alpine') {
                    sh '''
                    echo -e "https://alpine.global.ssl.fastly.net/alpine/v3.18/community" > /etc/apk/repositories
                    echo -e "https://alpine.global.ssl.fastly.net/alpine/v3.18/main" >> /etc/apk/repositories
                    apk update
                    apk add --no-cache binutils go postgresql-client git openssh
                    '''
                    sh 'git config --global --add safe.directory .'
                    sh 'go build -o gogs -buildvcs=false'
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
