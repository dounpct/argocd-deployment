# Add ArgoCD helm repo

    helm repo add jenkins https://charts.jenkins.io
    helm show values jenkins/jenkins

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

    helm template jenkins -n cicd . -f values/values.yaml | argocd-vault-plugin generate -
    helm template jenkins -n cicd . -f values/values.yaml | argocd-vault-plugin generate - | kubectl -n jenkins apply -f -


# get admin password
    kubectl get secret jenkins -n cicd -o jsonpath="{.data.jenkins-admin-password}" | base64 -d
# note for helm render

    helm template jenkins -n cicd . -f values/values.yaml | grep "# Source"

    helm template jenkins -n cicd . -f values/values.yaml -s templates/istio.yaml | argocd-vault-plugin generate -
    helm template jenkins -n cicd . -f values/values.yaml -s charts/jenkins/templates/jenkins-controller-statefulset.yaml | argocd-vault-plugin generate -
