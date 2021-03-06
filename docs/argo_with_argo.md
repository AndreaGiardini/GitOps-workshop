# Manage ArgoCD ... with ArgoCD!

It is possible to let ArgoCD manage itself! This is actually the recommended way of deploying ArgoCD, as described in the [docs](https://argoproj.github.io/argo-cd/operator-manual/declarative-setup/#manage-argo-cd-using-argo-cd).

## Installing Tools

ArgoCD supports several ways of generating Kubernetes manifests.
Defining manifests is supported using different [Tools](https://argoproj.github.io/argo-cd/user-guide/application_sources/).

ArgoCD is very unopinionated and leaves the operator total freedom regarding
which tool to use to generate manifests. We will see:

* Kustomize
* Helm
* Plain YAML

### Kustomize

In the previous step, we installed ArgoCD manually using [Kustomize](https://github.com/kubernetes-sigs/kustomize).
In order to let ArgoCD manage itself, we need to make sure that it is able to run Kustomize

Open the `argocd_deploy/kustomization.yaml` file and uncomment the following section:

```
#patchesStrategicMerge:
#- overlays/argocd-cm.yaml
#- overlays/argocd-repo-server-deploy.yaml
```

After applying this change:
* Kustomize will be downloaded on boot
* ArgoCD will allow us to use it in our manifests

Apply the changes to the ArgoCD deployment by running:

```
$ docker run --rm -v $(pwd)/argocd_deploy:/app/manifests k8s.gcr.io/kustomize/kustomize:v3.10.0 build manifests | kubectl apply -f -
```

At this point, Kustomize is installed in our ArgoCD instance and ready to be used.
ArgoCD tools are useful also to test upgrades.

## Add ArgoCD to the managed applications

All the tools needed are now installed and we are ready to let ArgoCD manage itself.
Whenever possible we should define our applications in a declarative way, the same applies for ArgoCD

Find the ArgoCD definition at `argocd_deploy/apps/argocd.yaml`
Take some time to have a look at the file and replace the fields that need to be edited.

When you're done, uncomment this section from `argocd_deploy/kustomization.yaml`:

```
resources:
- resources/namespace.yaml
- apps/argocd.yaml
```

Important: Commit all the changes in your repository and push it to your custom fork

Then apply the changes manually for the last time:

```
$ docker run --rm -v $(pwd)/argocd_deploy:/app/manifests k8s.gcr.io/kustomize/kustomize:v3.10.0 build manifests | kubectl apply -f -
```

With this change, ArgoCD is now managing itself using the definition in our repository. Argo will clone our repo and verify that the resources deployed in the cluster correspond with the resources in our git repository.

Verify on ArgoCD interface that a new application has been installed and is working
