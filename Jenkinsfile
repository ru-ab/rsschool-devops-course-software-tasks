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
        stage('Deploy Node Exporter') {
            steps {
                container('helm') {
                  sh 'helm upgrade --install node-exporter bitnami/node-exporter'
                }
            }
        }

        stage('Deploy Kubernetes State Metrics') {
            steps {
                container('helm') {
                  sh 'helm repo add prometheus-community https://prometheus-community.github.io/helm-charts'
                  sh 'helm repo update'
                  sh 'helm upgrade --install kube-state-metrics prometheus-community/kube-state-metrics'
                }
            }
        }

        stage('Deploy Prometheus') {
            steps {
                container('helm') {
                    sh 'helm repo add bitnami https://charts.bitnami.com/bitnami'
                    sh 'helm repo update'
                    sh 'helm upgrade --install prometheus bitnami/prometheus -f ./values.yaml'
                }
            }
        }
    }
}
