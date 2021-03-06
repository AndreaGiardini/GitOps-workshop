# Monitoring? Let's do it!

In the next example, we are going to use Helm instead of Kustomize to install our monitoring stack.
Have a look at the manifests in the `app_kube-prometheus-stack` folder, you should recognize the classic Helm structure

To install the monitoring stack we need to:

* Modify `argocd_deploy/apps/kube_prometheus_stack.yaml`
* Include it in the kustomization resources:

```
resources:
- resources/namespace.yaml
- apps/argocd.yaml
- apps/kube_prometheus_stack.yaml
```
* Apply the changes by committing and pushing

Kube-prometheus-stack is now being installed. You can follow the progress with:

```
$ kubectl get po -n monitoring -w
```

## Visualize grafana dashboard

Set up port-forward to grafana:

```
$ kubectl port-forward -n monitoring svc/kube-prometheus-stack-grafana 10000:80
```

Get admin password:

```
$ kubectl get secret --namespace monitoring kube-prometheus-stack-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

Login at [localhost:10000](http://localhost:10000) with username `admin`
and the password from the previous step

In Grafana you should find a dashboard dedicated to ArgoCD. Take some time
to explore its functionalities.
