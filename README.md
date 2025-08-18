```sh
argocd repo add https://github.com/rmsz005/sabkhi_lab_gitops.git --name gitops
argocd repo add https://github.com/rmsz005/sabkhi_lab_values.git --name values
```

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/rmsz005/sabkhi_lab_gitops/main/apps/bootstrap.yml

```