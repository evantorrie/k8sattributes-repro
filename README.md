## Instructions to reproduce

### Using opentelemetry collector 0.54.0

1. `brew install docker kind kubenetes-cli helm`
2. `kind create cluster --config kind-1worker-config.yaml`
3. `helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts`
4. `helm install my-otel-demo open-telemetry/opentelemetry-demo`
5. `helm install --values values.agent.yaml agent open-telemetry/opentelemetry-collector`
6. `helm install --values values.gateway.yaml gateway open-telemetry/opentelemetry-collector`
7. `kubectl patch deployment/my-otel-demo-frontend --type json --patch-file patch-my-otel-demo-1.yaml`
8. `kubectl set env deployment/my-otel-demo-frontend OTEL_EXPORTER_OTLP_ENDPOINT='http://$(K8S_NODE_IP):4317`
9. `kubectl port-forward svc/my-otel-demo-frontend 8080:8080`
10. `kubectl logs -f deployment/gateway-opentelemetry-collector`

* Go to your browser and access `http://localhost:8080`

* Watch for the traces coming in, and note that the debug logs from
`k8sattributesprocessor` indicates that it has matched the pod IP from
`k8s.pod.ip` and is adding the requested attributes to the resource
associated with the traces.

## Using opentelemetry collector 0.55.0

* Edit values.gateway.yaml and replace `image.tag` by "0.55.0"
* Run `helm upgrade gateway --values values.gateway.yaml
  open-telemetry/opentelemetry-collector`
* Run the same `kubectl logs -f
  deployment/gateway-opentelemetry-collector` and now note that the
  k8sattributesprocessor can no longer match the incoming pod IP
  specified in `k8s.pod.ip`

