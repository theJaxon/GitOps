### Installation:
- [Argo Helm chart](https://github.com/argoproj/argo-helm/tree/master/charts/argo-cd) is used for installation 
- Instead of port-forwarding, the service type for argocd-server was changed to NodePort which is set to port **30080** by default.
- For the rest of the instructions i've followed [Elton Stoneman ECS-C3: GitOps with Kubernetes and Argo](https://www.youtube.com/watch?v=e3oRY_OCoF0) YouTube video

```bash
# Create argocd namespace
k create ns argocd 

# Deploy argocd using helm
helm install argocd argo/argo-cd -n argocd --set server.service.type=NodePort

# Get the initial password (username is admin)
k -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

# Installing argocd CLI

```

---

### ArgoCD CLI commands:
```bash
# The port here is the default server NodePort referenced eariler
argocd login localhost:30080 --insecure --username admin --password <passwd>

# Used to get the name of the context 
k config get-contexts

# Add Vagrant kubernetes cluster in Argo
argocd cluster add kubernetes-admin@kubernetes

# Deploy apod using argocd 
argocd app create apod \
--repo https://github.com/theJaxon/GitOps.git \
--path sample_apps/apod \
--dest-server https://kubernetes.default.svc \
--dest-namespace apod

# Get current app status 
argocd app get apod

k create ns apod

# Reflect desired state from GitHub into the cluster
argocd app sync apod

# Optional: Make argo Auto Sync any changes in the GitHub repo 
argocd app set apod --sync-policy automated
```

