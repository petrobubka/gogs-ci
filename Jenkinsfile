pipeline {
    agent {
        kubernetes {
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
        stage('Install dependencies') {
            steps {
                container('alpine') {
                    sh '''
                    echo -e "https://alpine.global.ssl.fastly.net/alpine/v3.18/community" > /etc/apk/repositories
                    echo -e "https://alpine.global.ssl.fastly.net/alpine/v3.18/main" >> /etc/apk/repositories
                    apk update
                    apk add --no-cache binutils go postgresql-client git openssh
                    '''
                }
            }
        }
        
        stage('Build Gogs') {
            steps {
                container('alpine') {
                    sh 'go build -o gogs -buildvcs=false'
                }
            }
        }
        
        stage('Test Gogs') {
            steps {
                container('alpine') {
                    sh 'go test -v -cover ./...'
                }
            }
        }
    }
}
