# Notifications

The final lab of this workshop adds notifications to the monitoring app
that we just deployed. What we would like to achive is: trigger a webhook
every time a sync action is completed successfully.

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
`argocd` namespace prefixed with `argocd-notifications-controller-[...]`

## Configuring notifications

At this point, ArgoCD is running in our system ... But it is still
unconfigured.

In order to receive webhooks, we need to have a webhook url! Fortunately,
there are many websites online that allow you to test webhooks.

Head to [webhook.site](https://webhook.site) to get a unique webhook that
you will use for the rest of this workshop. The webhook URL is displayed
right below `Your unique URL`. Copy the URL by clicking on `Copy to
clipboard`.

Every request that we are going to send to that URL is going to be
displayed on webhook.site. This is a great way to test if our notification
system is working correctly!

To configure the webhook in ArgoCD, open the file
`argocd_deploy/overlays/webhook_notification.yaml` and replace the `url`
fields with the one that you just copied.

After that, uncomment the following line in
`argocd_deploy/kustomization.yaml`:

```
patchesStrategicMerge:
- overlays/webhook_notification.yaml
```

Then commit and push to apply the change.

We now configured our webhook notification... but we have no triggers!

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
    notifications.argoproj.io/subscribe.on-sync-succeeded.webhook-site: "webhook-site-notification"
```

By setting this annotation we are asking ArgoCD to send a notification to
Discord every time there is a successful sync on the
`kube-prometheus-stack` application.

Let's commit and push our changes

After the change is applied and the sync is completed we should see a new
webhook delivered!
