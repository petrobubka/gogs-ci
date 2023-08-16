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
    stage('Build') {
      steps {
        sh 'docker build -t petrobubka/my_gogs_image -f Dockerfile_app .'
      }
    }
    stage('Login') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
      }
    }
    stage('Push') {
      steps {
        sh 'docker push lloydmatereke/my_gogs_image'
      }
    }
  }
  post {
    always {
      sh 'docker logout'
    }
  }
}
