pipeline {
    agent {
        kubernetes {

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
                  - name: kubectl
                    image: joshendriks/alpine-k8s
                    command:
                    - /bin/cat
                    tty: true    
                  - name: kaniko
                    image: gcr.io/kaniko-project/executor:debug
                    command:
                    - /busybox/cat
                    tty: true
                    volumeMounts:
                      - name: kaniko-secret
                        mountPath: /kaniko/.docker
                  volumes:
                    - name: kaniko-secret
                      secret:
                        secretName: regcred
                        items:
                          - key: .dockerconfigjson
                            path: config.json
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
        
        stage('Kaniko Build & Push Image') {
              steps {
                container('kaniko') {
                  script {
                    sh '''
                    /kaniko/executor --dockerfile `pwd`/Dockerfile_app \
                                     --context `pwd` \
                                     --destination=petrobubka/my_gogs_image:${BUILD_NUMBER}
                    '''
                  }
                }
              }
            }

    }
}
