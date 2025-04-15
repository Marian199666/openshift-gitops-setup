OpenShift-GitOps-Setup anwenden:
kubectl apply -k managers/gitops-manager/base
Anleitung:
https://www.opensourcerers.org/2022/03/07/oops-something-is-wrong-with-your-gitops-application/
Vault installieren:
$ helm upgrade --install vault vault \
    --repo https://helm.releases.hashicorp.com \
    --namespace vault \
    --create-namespace \
    --set "global.openshift=true" \
    --set "server.dev.enabled=true" \
    --set "injector.enabled=false"

Vault Kubernetes Authentication and write the Kubernetes config:
$ oc -n vault rsh vault-0 
$ vault auth enable kubernetes
vault write auth/kubernetes/config \
  token_reviewer_jwt="$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" \
  kubernetes_host="https://$KUBERNETES_PORT_443_TCP_ADDR:443" \
  kubernetes_ca_cert=@/var/run/secrets/kubernetes.io/serviceaccount/ca.crt

Vault deinstallieren:
helm uninstall vault