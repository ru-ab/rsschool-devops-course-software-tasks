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
              - name: kubectl
                image: bitnami/kubectl:latest
                command:
                  - "/bin/sh"
                  - "-c"
                  - "sleep 99d"
                tty: true
                securityContext:
                  runAsUser: 0
              restartPolicy: Never
          """
        }
    }

    stages {
        stage('Deploy Node Exporter') {
            steps {
                container('helm') {
                  sh 'helm repo add bitnami https://charts.bitnami.com/bitnami'
                  sh 'helm repo update'
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
                    sh 'helm upgrade --install prometheus bitnami/prometheus -f ./prometheus-values.yaml'
                }
            }
        }

        stage('Deploy Grafana') {
            steps {
                container(name: 'kubectl', shell: '/bin/sh') {
                    withCredentials([string(credentialsId: 'GRAFANA_ADMIN_PASSWORD', variable: 'ADMIN_PASSWORD')]) {
                        sh '''
                            kubectl delete secret grafana-admin-secret --ignore-not-found
                            kubectl create secret generic grafana-admin-secret --from-literal=password=$(echo -n "$ADMIN_PASSWORD" | base64)
                        '''
                    }
                }

                container('helm') {
                    sh '''
                        helm repo add bitnami https://charts.bitnami.com/bitnami
                        helm repo update
                        helm upgrade --install grafana bitnami/grafana -f ./grafana-values.yaml
                    '''
                }
              
            }
        }
    }
}
