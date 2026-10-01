# Troubleshooting

## 1. CRD rollouts.argoproj.io faltante

- Sintoma: no matches for kind "Rollout" in version "argoproj.io/v1alpha1"
- Causa: El CRD principal de Argo Rollouts no se instalo correctamente.
- Solucion:

    kubectl apply --server-side=true --force-conflicts -k https://github.com/argoproj/argo-rollouts/manifests/crds?ref=stable

- Verificacion: kubectl get crd | grep argoproj

## 2. Limite de 256 KB en anotaciones

- Sintoma: metadata.annotations: Too long: must have at most 262144 bytes
- Causa: Client-Side Apply intenta guardar el manifiesto completo en una anotacion.
- Solucion: Server-Side Apply con --server-side=true --force-conflicts.

## 3. Race condition en port-forward

- Sintoma: curl: (7) Failed to connect to localhost port 8080
- Causa: El tunel no esta listo al ejecutar curl.
- Solucion: Pod efimero dentro del cluster:

    kubectl run curl-test --image=curlimages/curl --rm -it --restart=Never -- \
      curl -s http://rollout-bluegreen-active/color
