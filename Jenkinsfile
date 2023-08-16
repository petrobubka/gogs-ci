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
        stage('Deploy App') {
      steps {
        script {
          kubernetesDeploy(configs: "myweb.yaml", kubeconfigId: "mykubeconfig")
        }
      }
    }
    }
