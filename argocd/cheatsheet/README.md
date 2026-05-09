# ArgoCD CheatSheet

## 1. Set password for local user

### Approach 1: Using CLI (imperative)

```bash
argocd account update-password \
  --account=<account_name> \
  --new-password=<new_password>
```

### Approach 2: Declarative (bcrypt hash into `argocd-secret`)

```bash
# Create bcrypt hash from password
argocd account bcrypt --password <new_password>

# Path argocd-secret with hash just created
kubectl -n argocd patch secret argocd-secret \
  -p '{"stringData": {
    "accounts.alice.password": "<BCRYPT_PASSWORD>",
    "accounts.alice.passwordMtime": "'$(date -u +"%Y-%m-%dT%H:%M:%SZ")'"
  }}'
```

## 2. Create user automation with apiKey capability

```bash
kubectl patch -n argocd cm argocd-cm \
  --type='merge' \
  -p='{"data": {"accounts.automation": "apiKey"}}'
```