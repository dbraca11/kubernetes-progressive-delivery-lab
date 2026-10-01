# Kubernetes Progressive Delivery Lab

Laboratorio practico de estrategias de despliegue progresivo sobre Kubernetes usando Argo Rollouts.

Ejecutado en entornos 100% gratuitos (Killercoda + Argo Rollouts + GitHub), sin infraestructura local.

## Estrategias implementadas

- [x] Blue/Green Deployment - Cambio atomico de trafico con promocion manual y rollback.
- [ ] Canary Deployment - Proximamente (pesos graduales + analisis con Prometheus).

## Stack

- Kubernetes (Killercoda playground)
- Argo Rollouts
- kubectl
- GitHub Container Registry (GHCR)

## Estructura

    kubernetes-progressive-delivery-lab/
    |-- kubernetes/
    |   `-- blue-green/
    |       `-- rollout-bluegreen.yaml
    |-- docs/
    |   |-- blue-green.md
    |   `-- troubleshooting.md
    `-- README.md

## Como reproducirlo

1. Abrir el playground de Kubernetes en Killercoda.
2. Instalar Argo Rollouts y sus CRDs (ver docs/troubleshooting.md).
3. Aplicar los manifiestos de kubernetes/blue-green/.
4. Seguir la guia paso a paso en docs/blue-green.md.
