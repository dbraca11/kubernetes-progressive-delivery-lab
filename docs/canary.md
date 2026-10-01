# Canary Deployment con Argo Rollouts

## Objetivo

Implementar un despliegue Canary sobre Kubernetes con Argo Rollouts:
- Dos versiones vivas simultáneamente (stable + canary).
- Cambio gradual de tráfico: 10% -> 25% -> 50% -> 100%.
- Promocion manual con `kubectl argo rollouts promote`.
- Rollback instantaneo con `kubectl argo rollouts abort`.

## Flujo completo ejecutado

| Fase              | Comando                        | Weight | Estado  |
|-------------------|--------------------------------|--------|---------|
| Inicial           | -                              | 100    | Healthy |
| Patch a Green     | kubectl patch                  | 10     | Paused  |
| Promocion 1/3     | kubectl argo rollouts promote  | 25     | Paused  |
| Promocion 2/3     | kubectl argo rollouts promote  | 50     | Paused  |
| Promocion 3/3     | kubectl argo rollouts promote  | 100    | Healthy |

## Comandos clave

    kubectl apply -f kubernetes/canary/rollout-canary.yaml
    kubectl argo rollouts get rollout rollout-canary
    kubectl argo rollouts promote rollout-canary
    kubectl argo rollouts abort rollout-canary

## Evidencia visual

Se capturaron 5 pantallas del Dashboard de Argo Rollouts mostrando
la progresion del trafico: 100% -> 10% -> 25% -> 50% -> 100%.

## Observacion tecnica

Con 4 replicas, `setWeight: 10` produce un `ActualWeight: 20`, ya que
no se puede dividir el trafico entre fracciones de pods. En produccion
se usan mas replicas o una malla de servicio (Istio, Linkerd) para
controlar el peso a nivel de peticion.

## Dashboard

El Dashboard de Argo Rollouts se lanza con:

    kubectl argo rollouts dashboard

Accesible en http://localhost:3100.

## Rollback

### Rollback durante el Canary (abort)

Si se detecta un problema mientras el Canary esta en progreso (Paused):

    kubectl argo rollouts abort rollout-canary

Efecto: revierte TODO el trafico a la version estable inmediatamente,
sin pasar por los pasos intermedios. Estado resultante: Degraded.

### Rollback despues de promocion completa (undo)

Si el Canary ya se promociono al 100% y se quiere volver atras:

    kubectl argo rollouts undo rollout-canary
    kubectl argo rollouts promote rollout-canary --full

Efecto: crea una nueva revision con la imagen anterior y la promociona
completamente, restaurando la version estable anterior.
