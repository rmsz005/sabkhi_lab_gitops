## kubeconfig contexts: read-only and admin

This guide shows how to keep two contexts in one kubeconfig: a default read-only context and an admin (break-glass) context.

### 1) Prereqs (after Argo syncs)
- ServiceAccount `readonly-user` in namespace `platform-access` with a cluster-wide read-only role and binding (managed via GitOps in `rbac-readonly/`).

### 2) Get a short-lived token (Kubernetes >= 1.24)
```bash
kubectl -n platform-access create token readonly-user --duration=24h
```

### 3) Add a read-only context to your kubeconfig
Replace placeholders where needed.
```bash
# Optional: capture your current cluster name (depends on your setup)
CURRENT_CTX=$(kubectl config current-context)
CLUSTER_NAME=${CURRENT_CTX#*@}       # e.g., cluster.local
TOKEN=<paste token from step 2>

# Create a user and context that use the read-only token
kubectl config set-credentials homelab-readonly --token="$TOKEN"
kubectl config set-context homelab-readonly \
  --cluster="$CLUSTER_NAME" \
  --user=homelab-readonly \
  --namespace=default
```

### 4) Keep your admin context
Your existing admin context remains (e.g., `kubernetes-admin@cluster.local`). Optionally rename it for clarity:
```bash
kubectl config rename-context $(kubectl config current-context) homelab-admin
```

### 5) Switch between contexts
```bash
# Use read-only for day-to-day
kubectl config use-context homelab-readonly

# Switch to admin for emergencies
kubectl config use-context homelab-admin
```

### 6) Rotate the read-only token
Tokens created with `kubectl create token` are short-lived. When expired, generate a new one and re-run step 3 with the new token.

### Useful read-only commands
```bash
# What context and user am I using?
kubectl config current-context
kubectl config view --minify -o jsonpath='{.users[0].name}{"\n"}'

# Explore resources safely
kubectl get ns
kubectl get all -A
kubectl describe deploy -n <ns> <name>

# Kyverno policy reports
kubectl get clusterpolicyreport
kubectl get policyreport -A
kubectl describe clusterpolicyreport

# Argo CD Applications
kubectl -n argocd get applications.argoproj.io
```

Notes
- The read-only role grants get/list/watch on all resources. If you prefer to hide Secrets, we can remove `secrets` from the role.
- Consider keeping the admin context in a separate kubeconfig file and using the read-only context as default.
