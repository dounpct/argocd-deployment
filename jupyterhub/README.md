# Add ArgoCD helm repo

    helm repo add jupyterhub https://jupyterhub.github.io/helm-chart/
    helm repo update
    helm show values jupyterhub/jupyterhub

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

    helm template jupyterhub -n jupyterhub . -f values/values.yaml | argocd-vault-plugin generate -
    helm template jupyterhub -n jupyterhub . -f values/values.yaml | argocd-vault-plugin generate - | kubectl -n jupyterhub apply -f -


# note for helm render

    helm template jupyterhub -n jupyterhub . -f values/values.yaml | grep "# Source"

    helm template jupyterhub -n jupyterhub . -f values/values.yaml -s templates/istio.yaml | argocd-vault-plugin generate -
