pipeline {
    agent  {
        kubernetes {
          yaml """
            kind: Pod
            metadata:
              name: agent
            spec:
              containers:
              - name: helm
                image: alpine/helm:3.12.0
                command:
                - cat
                tty: true
              restartPolicy: Never
          """
        }
    }

    stages {
        stage('Deploy Prometheus') {
            steps {
                container('helm') {
                    sh 'helm repo add bitnami https://charts.bitnami.com/bitnami'
                    sh 'helm repo update'
                    sh 'helm upgrade --install prometheus bitnami/prometheus'
                }
            }
        }
    }
}