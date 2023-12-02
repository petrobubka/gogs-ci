pipeline {
    agent {
        kubernetes {

            yaml """
                apiVersion: v1
                kind: Pod
                spec:
                  containers:
                  - name: kubectl
                    image: joshendriks/alpine-k8s
                    command:
                    - /bin/cat
                    tty: true    
            """
        }
    }
    stages {
        stage('Deploy App to Kubernetes') {     
              steps {
                container('kubectl') {
                  withCredentials([file(credentialsId: 'mykubeconfig', variable: 'KUBECONFIG')]) {
                    sh 'kubectl delete deployment gogs'
                    sh 'sed -i "s/<TAG>/${BUILD_NUMBER}/" gogs-deployment.yaml'
                    sh 'kubectl apply -f gogs-deployment.yaml -n default'
                  }
                }
              }
            }
    }
}
