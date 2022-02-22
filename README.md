This demo presents an use case of argocd-notifications controller on a custom Argo CD instance together with vault management using argocd-vault-plugin.

## Vault installation
```
oc new-project vault
oc project vault

helm repo add hashicorp https://helm.releases.hashicorp.com

helm install vault hashicorp/vault \
  --set "global.openshift=true" \
  --set "server.dev.enabled=true"
```
After the vault instance is installed, we stil need to finish a few steps for the authentication and policy. More details can be found in [vault configuration](https://github.com/StinkyBenji/openshift-gitops-setup/blob/master/manifest/hashicorp-vault/README.md).


## OpenShift GitOps installation
- https://github.com/siamaksade/openshift-gitops-getting-started

You can simply install OpenShift GitOps operator on OperatorHub. 

In this demo, we create a new argo CD instance to deploy argocd-notifications controller. More details can be found in [Argo CD configuration](https://github.com/StinkyBenji/openshift-gitops-setup/blob/master/argocd-server/README.md)

For argocd-notifications controller, more info can be found in [this Repo](https://github.com/StinkyBenji/notifications-demo).

## Deploy 
`oc apply -k managers/gitops-manager/base`

## More to read
- https://github.com/lcolagio/lab-vault-plugin
- https://argocd-notifications.readthedocs.io/en/stable/
