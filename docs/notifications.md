# Notifications

The final lab of this workshop adds notifications to the monitoring app
that we just deployed. What we would like to achive is: get a Discord
message every time a sync action is completed successfully.

## Installing ArgoCD notifications

As we mentioned before, ArgoCD notifications is a separate and optional
components. It is not installed by default and it is not needed for ArgoCD
to work correctly.

To install Argo CD notifications we need to:

* Open the `argocd_deploy/kustomization.yaml` file
* Uncomment the following sections, marked as `ArgoCD Notifications`

```
resources:
- https://raw.githubusercontent.com/argoproj-labs/argocd-notifications/[...]

patchesStrategicMerge:
- https://raw.githubusercontent.com/argoproj-labs/argocd-notifications/[...]
```

* Commit and push to the master branch of our repository

At this point, ArgoCD will take care of installing ArgoCD notifications.
At the end of the installation you should see a new pod running in the
`argocd` namespace starting with `argocd-notifications-controller-[...]`

## Configuring notifications

At this point, ArgoCD is running in our system ... But it is still
unconfigured.

Open the file `argocd_deploy/overlays/discord_webhook_notification.yaml`
and replace the `myUsername` and `url` fields with the ones provided by
the instructor.

After that, uncomment the following line in
`argocd_deploy/kustomization.yaml`:

```
patchesStrategicMerge:
- overlays/discord_webhook_notification.yaml
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
    notifications.argoproj.io/subscribe.on-sync-succeeded.discord: "discord-notification"
```

By setting this annotation we are asking ArgoCD to send a notification to
Discord every time there is a successful sync on the
`kube-prometheus-stack` application.

Let's commit and push our changes

After the change is applied and the sync is completed we should see a new
message posted by our bot in the Discord channel!














