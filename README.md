## Usage
### Create a GKE cluster

Create a GKE cluster as per Google Container Engine docs. Pull down the kubeconfig and all credentials.  
Setup a separate service account for docker registry and pull down its json key

### Install Kubernetes Helm

Build Helm from source, as described in https://github.com/kubernetes/helm  
Run

```
helm init -c
```

Then run tiller locally in a separate terminal

```
tiller
```
### Create required Kubernetes secrets

Create the two secrets required by the chart:

````
kubectl create secret generic gce-key-secret --from-file=keyfile=<PATH TO YOUR DOCKER REGISTRY SERVICE ACCOUNT KEY> --namespace=helm
kubectl create secret generic registry-http-secret --from-literal=secret=<SOME RANDOM STRING OF CHARACTERS> --namespace=helm
````

### Install the chart

Install the chart by running 

```
helm install .
```

from repository's folder

### Wait for cluster external IP to create

Run

```
kubectl get service docker-registry --namespace=helm --watch
```

and wait for EXTERNAL-IP to fill out

### Make sure your docker machine engine works with insecure registries

Create a docker-machine instance with 
```
'--engine-insecure-registry "<EXTERNAL-IP>:5000"' 
```
parameter. If you are not using docker machine, just modify your docker daemon setting with the same switch, and restart it.

### Try out an image

Tag a docker image with 
```
<EXTERNAL-IP>:5000/your/image
```

and then push it with 
```
docker push <EXTERNAL-IP>:5000/your/image
```