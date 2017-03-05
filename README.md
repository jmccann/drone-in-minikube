# Running Drone in Kuberenetes with Gitea

This is an effort to make it simpler to run Drone in Kuberenetes locally for
developing patterns/solutions/drivers for Drone and kuberenetes.

# Install minikube

Tool to easily install/setup/manage kubernetes inside virtualbox

https://github.com/kubernetes/minikube

# Install kubectl

CLI tool for kubernetes

https://kubernetes.io/docs/user-guide/prereqs/

# Create local kubernetes environment

```
minikube start --cpus 6 --memory 5000
# wait a few minutes

# Setup docker env to use docker in minikube vbox instance
eval #(minikube docker-env)

# See stuff running
kubectl get all
docker ps
```

# Install/Configure gitea

```
kubectl apply -f gitea-deployment.yaml
kubectl apply -f gitea-service.yaml

# Wait a few minutes for gitea to download and start
kubectl get all
kubectl describe pod gitea

# Get URL to access gitea when everything is up
minikube service gitea --url
```

We will also need to set a host entry for `gitea` locally.

```
echo "$(minikube ip) gitea" >> /etc/hosts
```

Now login to your gitea instance (from `minikube service gitea --url` above) to finish setting it up.  Create yourself an admin account as well.

* `Database Type`: sqlite3
* `Application URL` should match output of `minikube service gitea --url`

# Install Drone

```
kubectl apply -f drone-secrets.yaml
kubectl apply -f drone-configmap.yaml
kubectl apply -f drone-server-deployment.yaml
kubectl apply -f drone-server-service.yaml
kubectl apply -f drone-agent-deployment.yaml
```

# Create a repo

1. Login to gitea - `minikube service gitea --url`
2. Create a repo
3. Login to Drone - `minikube service drone-server --url`
4. Activate your repo in Drone
5. **FIX YOUR WEBHOOK**
  * Go to webhook in gitea for repo you activated
  * Change to `http://drone-server:8000/hook?access_token=xxxxx`
6. Clone the gitea repo and have fun!
