```sh
argocd repo add https://github.com/rmsz005/sabkhi_lab_gitops.git --name gitops
argocd repo add https://github.com/rmsz005/sabkhi_lab_values.git --name values
```

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/rmsz005/sabkhi_lab_gitops/main/apps/bootstrap.yml

```

setup pihole wildcard dns for *internal.rmsz005.com
```sh
ssh root@pi
pihole-FTL --config misc.dnsmasq_lines '["address=/internal.rmsz005.com/192.168.1.240", "local=/internal.rmsz005.com/"]'
```