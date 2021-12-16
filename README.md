## Installing
# get the latest version or change to a specific version
VERSION=$(curl --silent "https://api.github.com/repos/argoproj-labs/argocd-autopilot/releases/latest" | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/')

# download and extract the binary
curl -L --output - https://github.com/argoproj-labs/argocd-autopilot/releases/download/$VERSION/argocd-autopilot-linux-amd64.tar.gz | tar zx

# move the binary to your $PATH
mv ./argocd-autopilot-* /usr/local/bin/argocd-autopilot

# check the installation
argocd-autopilot version

# forward ports from kubernetes
kubectl port-forward -n argocd svc/argocd-server 8080:80

## Creating an Argo project from scratch with 
# All of the commands need your git token with the --git-token flag,
# or the GIT_TOKEN env variable:

    export GIT_TOKEN=<YOUR_TOKEN>

# The commands will also need your repo clone URL with the --repo flag,
# or the GIT_REPO env variable:

    export GIT_REPO=<REPO_URL>

# 1. Run the bootstrap installation on your current kubernetes context.
# This will install argo-cd as well as the application-set controller.

    argocd-autopilot repo bootstrap

# Please note that this will automatically attempt to create a private repository,
# if the clone URL references a non-existing one. If the repository already exists,
# the command will just clone it.

# 2. Create your first project

    argocd-autopilot project create simpleapp

# 3. Install your first application on your project

    argocd-autopilot app create prod --app github.com/jhoelzel/simpleapp/kube-manifests/kustomize/overlays/prod -p simpleapp
    argocd-autopilot app create dev --app github.com/jhoelzel/simpleapp/kube-manifests/kustomize/overlays/dev -p simpleapp
