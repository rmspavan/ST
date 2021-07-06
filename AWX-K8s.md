Links:
https://github.com/ansible/awx-operator
https://github.com/ansible/awx-operat...

If you like the demo, hit subscribe for more videos like this and give it a like.

COMMANDS:

Setup the repo's:
curl -LO https://storage.googleapis.com/kubern...`curl -s https://storage.googleapis.com/kubern...`/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
sudo apt-get update -y &&  sudo apt-get install -y docker.io

Install minikube:
curl -Lo minikube https://storage.googleapis.com/miniku... && chmod +x minikube && sudo mv minikube /usr/local/bin/

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
kubectl apply -f https://raw.githubusercontent.com/ans...
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

Run the deployment:
kubectl apply -f awx-demo.yml
kubectl get pods -l "app.kubernetes.io/managed-by=awx-operator"
kubectl get svc -l "app.kubernetes.io/managed-by=awx-operator"
WAIT A FEW MINS... check again...

Get the Admin user password:
kubectl get secrets
kubectl get secret awx-demo-admin-password -o jsonpath="{.data.password}" | base64 --decode

Expose the deployment:
kubectl expose deployment awx-demo --type=LoadBalancer --port=8080

Enable AWX to be access via the Internet:
kubectl port-forward svc/awx-demo-service --address 0.0.0.0 30886:80
