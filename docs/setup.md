# Setup

In this section we will create a new Kubernetes cluster locally using kind and install ArgoCD on it.
We will then access the web interface to make sure that everything is in place.

## Create K8s cluster

Use [kind](https://github.com/kubernetes-sigs/kind) to start a new local cluster.

```
kind create cluster --image=kindest/node:v1.21.2
```

## Install ArgoCD

Now that the cluster is up and running, let's build the manifests of ArgoCD using kustomize and apply them.
Kustomize is a tool that generates Kubernetes manifests, starting from a definition.

In this case we are going to run Kustomize from a docker container, but you can also download kustomize locally and run it from there, the result shouldn't change.

```
docker run --rm -v $(pwd)/argocd_deploy:/app/manifests k8s.gcr.io/kustomize/kustomize:v4.2.0 build manifests | kubectl apply -f -
```

ArgoCD is now being installed. You can follow the progress with:

```
kubectl get po -n argocd -w
```

Take this time to have a look a the resources in the `GitOps-workshop/argocd_deploy` folder.
You will find some commented lines and files, we will use them in the next steps.

## Login web interface

Let's then verify that everything is up and running by accessing the web interface of ArgoCD.

* Port forward to the Argo CD server. This will bing your local port to ArgoCD  
  `kubectl port-forward svc/argocd-server -n argocd 8080:443`
* Get the auto-generated password for the 'admin' user  
  `kubectl get secret -n argocd argocd-initial-admin-secret --template={{.data.password}} | base64 --decode`
* Login on [localhost:8080](http://localhost:8080) with username `admin` and password from previous step

## Login CLI (optional)

Optionally, you can also install the ArgoCD CLI and then log in using:

`argocd login localhost:8080`

