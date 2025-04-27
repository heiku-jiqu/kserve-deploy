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

## Not validating kustomize
Kustomize files failed halfway, leading to half-patched state.
After fixing, resulted in [conflicts](https://kubernetes.io/docs/reference/using-api/server-side-apply/#conflicts) when applying again.
Needed to `--force-conflicts` to overwrite.

Should do `--dry-run` first.

## Not correctly patching
Had typo in config patches onto `configmap/inferenceservice-config`.
Needed to specify the `name` and `namespace` under `metadata` so it can patch properly.

## gRPC not working

`InferenceService.spec.predictor.triton.ports.name` NEEDS to be `grpc`/`h2c`/`h2c-port` so that `InferenceService` creates
a `Service` with `ports.[0].appProtocol` = `kubernetes.io/h2c`.
Check the port of the `Service.items.spec.ports` via `kubectl get svc -n <InferenceServiceNamespace> -o yaml`

gRPC InferenceService uses HTTPRoute underneath, does NOT use GRPCRoute from Gateway API. [see](https://docs.google.com/document/d/1Vis01baNOPgS0eQr3FsbnKZA980By-zd9Ytk3faNGls)

Name of model is based on the model names in your Triton Model Repository bucket. [example](https://console.cloud.google.com/storage/browser/kfserving-examples/models/torchscript)

gRPC authority is similar to `Host:` header.

`grpcurl -vv -plaintext -proto grpc_predict_v2.proto -authority torchscript-cifar10-predictor-kserve-triton.example.com -d @ localhost:8888 inference.GRPCInferenceService.ModelInfer <input-grpc.json`
