# Notifications

The final lab of this workshop adds notifications to the monitoring app
that we just deployed. What we would like to achive is: get a Discord
message every time a sync action is completed successfully.

## ArgoCD notifications

At this point, ArgoCD notifications is already running in our system ...
But it is still unconfigured.

Open the file `argocd_deploy/overlays/discord_notification.yaml`
and replace the `myUsername` and `url` fields with the ones provided by
the instructor.

After that, uncomment the following line in
`argocd_deploy/kustomization.yaml`:

```
patchesStrategicMerge:
- overlays/discord_notification.yaml
```

Then commit and push to apply the change.

We now configured our discord notification. Time to set up the triggers

## Configuring triggers

Using triggers we can define when we want to activate the notification
that we just set up. We define triggers by annotating the Application
resouce.

Open `argocd_deploy/apps/kube_prometheus_stack.yaml` and remove the
following comment:

```
kind: Application
metadata:
  name: kube-prometheus-stack
  namespace: argocd
  annotations:
    notifications.argoproj.io/subscribe.mytrigger.discord: ""
```

By setting this annotation we are asking ArgoCD to send a notification to
Discord every time there is a successful sync on the
`kube-prometheus-stack` application.

Let's commit and push our changes

After the change is applied and the sync is completed we should see a new
message posted by our bot in the Discord channel!
