# ArgoCD Demo

#### Prerequisites

- Kubernetes cluster ([minikube](https://minikube.sigs.k8s.io/docs/start) it's the one I used)
- [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/)
- [kustomize](https://kustomize.io/) (just to see how it works, ArgoCD takes care of the rest afterwards)

#### Commands

```bash
# Install ArgoCD in k8s
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Access ArgoCD UI
kubectl get svc -n argocd
kubectl port-forward svc/argocd-server 8080:443 -n argocd

# Login with admin user and below token (as in documentation):
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode && echo

# Create applications to control each environment
kubectl apply -f applications/ -n argocd
```

#### Sequence of events

After running the previous commands we've simulated a scenario with development, staging and production environments.

Suppose now `myapp` had a new feature developed. With the help of `kustomize` the process of updating the deployment of each environment is as follows:

- The CI pipeline is triggered in the development environment.
- The pipeline builds and pushes the new image to the registry.
- Once the image is pushed, the pipeline updates the `images[0].newTag` key in the corresponding overlay, through a commit and push in the DevOps repo (this one!).

This process can be executed again by another instances of the pipeline, once the changes are promoted to higher environments (CI pipeline for staging, CI pipeline for production, etc.)

#### Links

- Install ArgoCD: [https://argo-cd.readthedocs.io/en/stable/getting_started/#1-install-argo-cd](https://argo-cd.readthedocs.io/en/stable/getting_started/#1-install-argo-cd)

- Login to ArgoCD: [https://argo-cd.readthedocs.io/en/stable/getting_started/#4-login-using-the-cli](https://argo-cd.readthedocs.io/en/stable/getting_started/#4-login-using-the-cli)

- ArgoCD Configuration: [https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/)

- The Kustomization File: [https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/](https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/)
