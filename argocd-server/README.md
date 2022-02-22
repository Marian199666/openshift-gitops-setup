## Create a custom Argo CD instance
We want to create an Argo CD instance with [argocd-vault-plugin](https://github.com/argoproj-labs/argocd-vault-plugin) installed so that later we can interact with the vault instance created earlier and retrieve secrets from it.

`default/base` includes the basic manifest of an ArgoCD instance. In `argocd/overlays/argocd.yaml`, this section will install the argocd-vault-plugin on the Argo CD repo server using an init container (built into the operator) on a volume mount, which is also mounted to the actual repo container on an executable path (`/usr/local/bin/argocd-vault-plugin`).
```
- op: add
  path: /spec/repo
  value:
    # init container for argocd-vault-plugin
    initContainers:
      - command:
          - /bin/sh
          - '-c'
          - >-
            curl -v  -LJ --tls-max 1.2
            https://github.com/argoproj-labs/argocd-vault-plugin/releases/download/v1.8.0/argocd-vault-plugin_1.8.0_linux_amd64
            -o argocd-vault-plugin && chmod -v +x argocd-vault-plugin
        image: 'registry.redhat.io/ubi8/ubi:latest'
        name: install-vault
        volumeMounts:
          - mountPath: /vault-plugin
            name: vault-plugin
        workingDir: /vault-plugin
    mountsatoken: true
    serviceaccount: vplugin
    volumeMounts:
      - mountPath: /usr/local/bin/argocd-vault-plugin
        name: vault-plugin
        subPath: argocd-vault-plugin
    volumes:
      - emptyDir: {}
        name: vault-plugin  
```
Then we need to define the configManagementPlugins:

```
- op: add
  path: /spec/configManagementPlugins
  value: |-
    - name: argocd-vault-plugin
      generate:
        command: ["argocd-vault-plugin"]
        args: ["generate", "./"]
```

This custom plugin can be referred in Argo CD application definition:

```
source:
  plugin:
    name: argocd-vault-plugin
    ...
  repoURL: ....
```