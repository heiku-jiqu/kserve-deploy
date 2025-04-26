# kserve-deploy
For Kserve Deployments

# Apply

```sh
# validate
kubectl kustomize build ./raw-deployment
kubectl apply --dry-run=server -k ./raw-deployment

# actually apply
kubectl apply -k ./raw-deployment
```

# Issues faced

Kustomize files failed halfway, leading to half-patched state.
After fixing, resulted in [conflicts](https://kubernetes.io/docs/reference/using-api/server-side-apply/#conflicts) when applying again.
Needed to `--force-conflicts` to overwrite.

Should do `--dry-run` first.
