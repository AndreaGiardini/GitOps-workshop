# Kubernetes-GitOps

###############################################################################
# Initial setup
###############################################################################

## Create K8s cluster

* `kind create cluster --image=kindest/node:v1.19.7`

## Install ArgoCD version 1.8.5

* `k apply -f 0_ns_argocd.yml`
* `k apply -n argocd -f 1_argocd.yml`

### Wait some time, argocd needs to be installed
### Check with `k get po -A`

## Login web interface

* `kubectl port-forward svc/argocd-server -n argocd 8080:443`
* `kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server -o name | cut -d'/' -f 2` # Get admin password
* Login on localhost:8080 with admin:$prevPassword

## Login CLI

* `argocd login localhost:8080`

## Add running cluster to ArgoCD

* `argocd cluster add kind-kind --in-cluster`

###############################################################################
# Monitoring
###############################################################################

* `argocd app create kube-prometheus-stack --repo
  https://github.com/AndreaGiardini/GitOps-workshop.git --path
  app_kube-prometheus-stack --dest-server https://kubernetes.default.svc
  --dest-namespace monitoring --values values.yaml --sync-option
  CreateNamespace=true`

* `argocd app sync kube-prometheus-stack`

###############################################################################
# Grafana password & access
###############################################################################

* `kubectl get secret --namespace monitoring kube-prometheus-stack-grafana
  -o jsonpath="{.data.admin-password}" | base64 --decode ; echo`
* `k port-forward -n monitoring svc/kube-prometheus-stack-grafana 10000:80`

