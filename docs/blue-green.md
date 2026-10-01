# Blue/Green Deployment con Argo Rollouts

## Objetivo

Implementar un despliegue Blue/Green sobre Kubernetes usando Argo Rollouts:

- Dos entornos identicos en paralelo (Blue = produccion, Green = nueva version).
- Servicios separados (active y preview).
- Promocion manual (autoPromotionEnabled: false).
- Rollback controlado.

## Flujo completo ejecutado

| Fase           | Comando                        | active | Estado  |
|----------------|--------------------------------|--------|---------|
| Inicial        | -                              | blue   | Healthy |
| Patch a Green  | kubectl patch                  | blue   | Paused  |
| Promocion      | kubectl argo rollouts promote  | green  | Healthy |
| Rollback (1/2) | kubectl argo rollouts undo     | green  | Paused  |
| Rollback (2/2) | kubectl argo rollouts promote  | blue   | Healthy |

## Comandos clave

    kubectl argo rollouts get rollout rollout-bluegreen
    kubectl argo rollouts promote rollout-bluegreen
    kubectl argo rollouts undo rollout-bluegreen

Verificar trafico desde dentro del cluster:

    kubectl run curl-test --image=curlimages/curl --rm -it --restart=Never -- \
      curl -s http://rollout-bluegreen-active/color

## Observacion importante

Con autoPromotionEnabled: false, kubectl argo rollouts undo NO revierte el trafico inmediatamente. Crea una nueva revision con la imagen anterior, la expone en preview y pausa el Rollout. El rollback completo requiere undo + promote.
