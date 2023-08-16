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
      environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    }
    stages {
        stage('Install dependencies') {
            steps {
                container('alpine') {
                    sh '''
                    echo -e "https://alpine.global.ssl.fastly.net/alpine/v3.18/community" > /etc/apk/repositories
                    echo -e "https://alpine.global.ssl.fastly.net/alpine/v3.18/main" >> /etc/apk/repositories
                    apk update
                    apk add --no-cache binutils go postgresql-client git openssh docker
                    dockerd &
                    sleep 10  # Allow Docker daemon to start
                    '''
                }
            }
        }
        stage('Build') {
          steps {
            container('alpine'){
                sh 'docker build -t petrobubka/my_gogs_image -f Dockerfile_app .'
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push lloydmatereke/my_gogs_image'
                sh 'docker logout'
                }
            }
        }
    }
}
