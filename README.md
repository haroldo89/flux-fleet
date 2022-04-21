
flux demo

# Install Flux CLI by following the instructions from https://toolkit.fluxcd.io/guides/installation/#install-the-flux-cli

variables de entorno 
Replace `[...]` with the GitHub token
export GITHUB_TOKEN=[...]
Replace `[...]` with the GitHub organization or a GitHub user if it is a personal account
export GITHUB_USER=[...]
Replace `[...]` with the GitHub organization or a GitHub user if it is a personal account
export GITHUB_ORG=[...]
Replace `[...]` with `true` if it is a personal account, or with `false` if it is an GitHub organization
export GITHUB_PERSONAL=[...]


```bash

cd flux-fleet

flux create source git angular \
    --url https://github.com/$GITHUB_ORG/devops-app-angular \
    --branch master \
    --interval 30s \
    --export \
    | tee apps/angular.yaml

flux create kustomization angular \
    --source angular \
    --path "./" \
    --prune true \
    --validation client \
    --interval 10m \
    --export \
    | tee -a apps/angular.yaml

flux create source git nodejs \
    --url https://github.com/$GITHUB_ORG/devops-app-nodejs \
    --branch master \
    --interval 30s \
    --export \
    | tee apps/nodejs.yaml

flux create kustomization nodejs \
    --source nodejs \
    --path "./" \
    --prune true \
    --validation client \
    --interval 10m \
    --export \
    | tee -a apps/nodejs.yaml

flux create source git \
    devops-toolkit \
    --url https://github.com/$GITHUB_ORG/devops-app-flux-helm.git \
    --branch main \
    --interval 30s \
    --export \
    | tee apps/devops-toolkit.yaml


cd proyecto devops-app-angular

flux create helmrelease \
    devops-toolkit-angular \
    --source GitRepository/devops-toolkit \
    --values values.yaml \
    --chart "helm" \
    --target-namespace angular \
    --interval 30s \
    --export \
    | tee -a ../flux-fleet/apps/devops-toolkit.yaml

cd proyecto devops-app-nodejs

flux create helmrelease \
    devops-toolkit-nodejs \
    --source GitRepository/devops-toolkit \
    --values values.yaml \
    --chart "helm" \
    --target-namespace nodejs \
    --interval 30s \
    --export \
    | tee -a ../flux-fleet/apps/devops-toolkit.yaml

```