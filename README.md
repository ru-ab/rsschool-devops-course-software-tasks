# Task 4: Jenkins Installation and Configuration

## Install Kubernetes Cluster using k3s

1. Create k3s cluster on the control plane node:

```bash
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--tls-san X.X.X.X" sh -s - --write-kubeconfig-mode 644
```

## Install Helm

1. Run commands to install Helm on Debian/Ubuntu Linux:

```bash
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

2. Add ENV variable KUBECONFIG for the helm util:

```bash
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
```

3. Deploy the Nginx chart from Bitnami:

```bash
helm install my-release oci://registry-1.docker.io/bitnamicharts/nginx
```

4. Remove the Nginx chart from Bitnami:

```bash
helm uninstall my-release
```

## Install Jenkins

1. Add the Jenkins repo:

```bash
helm repo add jenkinsci https://charts.jenkins.io
helm repo update
```

2. Create a persistent volume (PV) using **jenkins-volume.yaml** configuration file:

```bash
kubectl apply -f jenkins-volume.yaml
```

3. Add permissions to the volume folder:

```bash
sudo chown -R 1000:1000 /data/jenkins-volume
```

4. Create namespace 'jenkins':

```bash
kubectl create namespace jenkins
```

5. Create a service account using **jenkins-sa.yaml** configuration file:

```bash
kubectl apply -f jenkins-sa.yaml
```

6. Install Jenkins by running the **helm install** command:

```bash
chart=jenkinsci/jenkins
helm install jenkins -n jenkins -f jenkins-values.yaml $chart
```

7. Get password for the admin user:

```bash
jsonpath="{.data.jenkins-admin-password}"
secret=$(kubectl get secret -n jenkins jenkins -o jsonpath=$jsonpath)
echo $(echo $secret | base64 --decode)
```

## Jenkins Authorization

1. Install **Role-based Authorization Strategy** plugin to define roles for users.
