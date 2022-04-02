
# Test - ArgoCD upgrade

But... Will it actually work?

To test it out we will let ArgoCD upgrate itself! The installed version is
v2.3.1 but v2.3.2 is out... let's upgrade!

Have a look at the `argocd_deploy/kustomization.yaml` file... Can you figure out how to do it?

Apply the change by committing and pushing to `master`, no need to use the docker command line anymore.
After a couple of minutes you should see the new version deployed!

If the waiting is too long, you can force Argo to refresh the definition
manually by opening the web interface and pressing the `Refresh` button
under the `argocd` application.

You might have to restart the `kubectl proxy` command in order to see the
new interface. That's because a new Argo Pod will be created.

It is possible for Argo to receive webhook from Github whenever a new
commit is pushed
([docs](https://argoproj.github.io/argo-cd/operator-manual/webhook/)).
However its configuration is more complex and out of scope for this
workshop. When webhooks are not configured, Argo will poll regularly the
git repo.

If you have done every step correctly, you should see `v2.3.2` in the top
left corner of the web interface, below Argo's logo.
