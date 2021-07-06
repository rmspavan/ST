https://github.com/ansible/awx-operator
https://github.com/ansible/awx-operator/tags

COMMANDS:

Setup the repo's:
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
sudo apt-get update -y &&  sudo apt-get install -y docker.io

Install minikube:
curl -Lo minikube https://storage.googleapis.com/minikube/release/latest/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/

sudo usermod -aG docker $USER
LOGOUT & BACK IN AGAIN
groups $USER

Start minikube:
minikube start --addons=ingress --cpus=2 --install-addons=true --kubernetes-version=stable --memory=6g

Check its working:
kubectl get nodes
kubectl get pods
kubectl get pods -A

Install the awx-operator:
kubectl apply -f https://raw.githubusercontent.com/ansible/awx-operator/0.10.0/deploy/awx-operator.yaml
kubectl get pods

Create the deployment file:
vi awx-demo.yml
---
apiVersion: awx.ansible.com/v1beta1
kind: AWX
metadata:
  name: awx-demo
spec:
  service_type: nodeport
  ingress_type: none
  hostname: awx-demo.example.com
  
kubectl get po
 
Run the deployment:
kubectl apply -f awx-demo.yml
kubectl get pods -l "app.kubernetes.io/managed-by=awx-operator"
kubectl get svc -l "app.kubernetes.io/managed-by=awx-operator"
WAIT A FEW MINS... check again...
kubectl get svc
minikube tunnel (ti check LB)


Get the Admin user password:
kubectl get secrets
kubectl get secret awx-demo-admin-password -o jsonpath="{.data.password}" | base64 --decode

Expose the deployment:
kubectl expose deployment awx-demo --type=LoadBalancer --port=8080

Enable AWX to be access via the Internet:
kubectl port-forward svc/awx-demo-service --address 0.0.0.0 30886:80
