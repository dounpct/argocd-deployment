# Add ArgoCD helm repo

    helm repo add awx-operator https://ansible.github.io/awx-operator/
    helm show values awx-operator/awx-operator

    helm dep update

# Install ArgoCD vault plugin

    curl -Lo argocd-vault-plugin https://github.com/argoproj-labs/argocd-vault-plugin/releases/download/v1.12.0/argocd-vault-plugin_1.12.0_linux_amd64
    chmod +x argocd-vault-plugin
    sudo mv argocd-vault-plugin /usr/local/bin

# Render Secret from GCP Secret Manager

    export GOOGLE_APPLICATION_CREDENTIALS="path-to-your-google-service-account-key/key.json"
    export AVP_TYPE=gcpsecretmanager

    argocd-vault-plugin generate ./
    argocd-vault-plugin generate ./values/test.yaml

    helm template awx-operator -n awx-operator . -f values/values.yaml | argocd-vault-plugin generate -
    helm template awx-operator -n awx-operator . -f values/values.yaml | argocd-vault-plugin generate - | kubectl -n awx-operator apply -f -


# note for helm render

    helm template awx-operator -n awx-operator . -f values/values.yaml | grep "# Source"

    helm template awx-operator -n awx-operator . -f values/values.yaml -s templates/istio.yaml | argocd-vault-plugin generate -

# get default admin password

    kubectl -n awx-operator get secret ansible-awx-admin-password -o jsonpath="{.data.password}" | base64 -d