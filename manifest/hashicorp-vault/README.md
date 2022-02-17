## HashiCorp Vault

### After the vault instance is set, we will need to complete a few steps to make the vault functioning.

### First, enter the pod

`oc rsh vault-server-0`

### Then, we need to enable Kubernetes Auth by

`vault auth enable kubernetes`

### and we should see

`Success! Enabled kubernetes auth method at: kubernetes/`

### Now we need to write the Kubernetes Config

`vault write auth/kubernetes/config token_reviewer_jwt="$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" kubernetes_host="https://$KUBERNETES_PORT_443_TCP_ADDR:443" kubernetes_ca_cert=@/var/run/secrets/kubernetes.io/serviceaccount/ca.crt`

### if succeeded, we should see

`Success! Data written to: auth/kubernetes/config`

### Now we can create our secret

`vault kv put secret/vplugin/yoursecret key="value"`

### To make sure that the secret exists,

`vault kv get secret/vplugin/yoursecret`

### Moreover, we need to create a policy for the secret

`vault policy write vplugin - <<EOF`

`path "secret/data/vplugin/yoursecret" { `

`capabilities = ["read"] `

`}`

`EOF`

### Finally, let's create the authentication role

Note that you will need to create the ServiceAccount (in this case, `vplugin` ) in the namespace (e.g. `argocd-01`) where the Argo CD instance is installed.

`vault write auth/kubernetes/role/vplugin bound_service_account_names=vplugin bound_service_account_namespaces=argocd-01 policies=vplugin ttl=24h`

### Now, we are all set!
