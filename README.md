# bikri-gitops — desired state; deploys are commits here

ArgoCD watches this repo (**pull-based** — nothing pushes to the cluster). A deploy is a one-line change:

```yaml
# apps/backend/overlays/local/kustomization.yaml
images:
  - name: ghcr.io/<org>/vy/sales-service
    newTag: v1.5.0-a1b2c3d        # ← bump this, commit, done
```

- CI in `bikri-kendra` / `EventManagement` opens a PR with the bump after each successful image build — **merging the PR is the deploy approval**.
- Rollback: `git revert` (or set the previous tag).
- Audit: `git log --oneline apps/`.
- Frontend and backend are separate ArgoCD Applications → fully independent deploys.

Layout: `apps/<backend|frontend>/base` (manifests) + `overlays/<local|cloud>` (namespace, DB host, image tags, replicas).

One-time wiring after the cluster exists (bootstrap repo installs ArgoCD):
```bash
kubectl apply -f argocd/                  # project + 2 Applications
kubectl -n bikri-local create secret generic backend-secrets \
  --from-literal=DB_USERNAME=bikri --from-literal=DB_PASSWORD=<pw> \
  --from-literal=JWT_SECRET=<random-64-chars>       # secrets NEVER live in this repo
```

Validate locally before committing: `kustomize build apps/backend/overlays/local`.
Replace `<org>`: `grep -rl '<org>' . | xargs sed -i 's/<org>/YOUR_GH_ORG/g'`
# bikri-kendra-gitops
# bikri-kendra-gitops
# bikri-kendra-gitops
