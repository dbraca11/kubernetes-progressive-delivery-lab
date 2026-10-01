# Kubernetes Progressive Delivery Lab

Laboratorio práctico de estrategias de despliegue progresivo sobre Kubernetes usando **Argo Rollouts**.

Ejecutado en entornos **100% gratuitos** (Killercoda + Argo Rollouts + GitHub), sin infraestructura local.

## 🎬 Demo interactiva

Dos visualizaciones interactivas del proyecto (HTML + JavaScript, sin dependencias):

| Demo | Descripción | Enlace |
|------|-------------|--------|
| 📊 **Conceptos** | Diagrama animado de las estrategias Blue/Green y Canary con simulaciones | [diagram.html](https://dbraca11.github.io/kubernetes-progressive-delivery-lab/diagram.html) |
| 🎥 **Walkthrough** | Recorrido paso a paso por los comandos reales ejecutados en Killercoda, con estado del cluster, barras de tráfico y troubleshooting | [proyecto.html](https://dbraca11.github.io/kubernetes-progressive-delivery-lab/proyecto.html) |

## 🎯 Qué estamos haciendo

Este proyecto implementa y documenta las dos estrategias principales de **progressive delivery** sobre Kubernetes usando **Argo Rollouts**, un controlador de código abierto usado en producción por empresas como Intuit, Google o Netflix.

El objetivo es demostrar, con comandos y capturas reales, cómo:

- **Blue/Green**: desplegar una nueva versión en paralelo, validarla en aislamiento y cambiar el tráfico de forma atómica con rollback instantáneo.
- **Canary**: desviar el tráfico de forma gradual (10% → 25% → 50% → 100%), validando cada paso antes de continuar.
- **Análisis automatizado**: revertir un despliegue sin intervención humana cuando una métrica de negocio falla (provider `web` + `AnalysisTemplate`).

Todo el laboratorio se ejecuta en entornos efímeros y gratuitos, y los resultados se versionan en este repositorio.

## 🛠️ Cómo hacerlo

### Prerrequisitos

- Navegador web (todo el laboratorio se ejecuta online)
- Cuenta de GitHub (gratuita)
- No se requiere instalar nada en local: ni Docker, ni Kubernetes, ni Minikube, ni WSL

### Pasos generales

1. Abrir el playground de Kubernetes en [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes).
2. Instalar Argo Rollouts y sus CRDs (ver [docs/troubleshooting.md](docs/troubleshooting.md) por problemas conocidos).
3. Aplicar los manifiestos de `kubernetes/blue-green/` o `kubernetes/canary/`.
4. Seguir las guías paso a paso:
   - [docs/blue-green.md](docs/blue-green.md) — estrategia Blue/Green
   - [docs/canary.md](docs/canary.md) — estrategia Canary y análisis automatizado
5. Ver el estado en el Dashboard de Argo Rollouts: `kubectl argo rollouts dashboard`.

### Comandos clave

```bash
# Instalar Argo Rollouts
kubectl create namespace argo-rollouts
kubectl apply --server-side=true --force-conflicts -k \
  https://github.com/argoproj/argo-rollouts/manifests/crds?ref=stable

# Blue/Green
kubectl apply -f kubernetes/blue-green/rollout-bluegreen.yaml
kubectl argo rollouts promote rollout-bluegreen
kubectl argo rollouts undo rollout-bluegreen

# Canary
kubectl apply -f kubernetes/canary/rollout-canary.yaml
kubectl argo rollouts promote rollout-canary

# Dashboard
kubectl argo rollouts dashboard
🧰 Tecnologías utilizadas
Tecnología	Uso
Kubernetes	Orquestación de contenedores (Killercoda playground)
Argo Rollouts	Controlador de despliegue progresivo (Blue/Green + Canary)
kubectl	CLI de Kubernetes y plugin kubectl-argo-rollouts
Killercoda	Entorno de práctica gratuito con Kubernetes real
GitHub Pages	Hosting de las demos interactivas en HTML/JS
GitHub	Control de versiones y portafolio
HTML/CSS/JavaScript	Demos animadas del proyecto (sin dependencias)
⚠️ No se usan servicios de pago: ni AWS, Azure, GCP, DigitalOcean, ni Docker Desktop.

📁 Estructura del repositorio
text
kubernetes-progressive-delivery-lab/
├── docs/
│   ├── blue-green.md              # Guía Blue/Green
│   ├── canary.md                  # Guía Canary + análisis
│   ├── troubleshooting.md         # Problemas reales y soluciones
│   ├── diagram.html               # Demo 1: conceptos animados
│   ├── proyecto.html              # Demo 2: walkthrough del proyecto
│   └── images/                    # Capturas del dashboard
├── kubernetes/
│   ├── blue-green/
│   │   └── rollout-bluegreen.yaml
│   └── canary/
│       ├── rollout-canary.yaml
│       ├── rollout-canary-automated.yaml
│       └── analysis-template.yaml
├── .gitignore
└── README.md
✅ Resultado final
Al completar el laboratorio se obtiene:

Blue/Green
Dos entornos idénticos en paralelo: servicio active (producción) y preview (validación).

Cambio atómico del tráfico de Blue a Green sin downtime.

Rollback controlado en 2 pasos (undo + promote).

Observación clave documentada: con autoPromotionEnabled: false, el undo no revierte el tráfico directamente; pasa por la fase preview.

Canary
Tráfico progresivo: 10% → 25% → 50% → 100% con pausas manuales.

Análisis automatizado con AnalysisTemplate: si la condición de negocio falla, Argo Rollouts aborta el despliegue y revierte a la versión estable sin intervención humana.

Evidencia visual capturada en el Dashboard de Argo Rollouts.

Problemas reales resueltos
CRD rollouts.argoproj.io faltante → instalación manual con Server-Side Apply.

Límite de 256 KB en anotaciones de etcd → --server-side=true --force-conflicts.

Race condition en port-forward → pods efímeros con curlimages/curl.

Error de DNS en Killercoda → uso de ClusterIP en AnalysisTemplate.

Detalles completos en docs/troubleshooting.md.

📸 Evidencia visual
Capturas del Dashboard de Argo Rollouts durante el laboratorio:

Captura	Descripción
https://docs/images/01-dashboard-empty.png	Estado inicial del dashboard
https://docs/images/02-canary-initial-blue.png	Canary con todo el tráfico en Blue
https://docs/images/03-canary-after-rollback.png	Estado tras el rollback (Revision 3 estable, Revision 2 retirada)
🏆 Aprendizajes clave
Diferencia entre deployment tradicional y progressive delivery.

Cuándo usar Blue/Green (cambio atómico, rollback instantáneo) vs Canary (validación con tráfico real).

Cómo funciona el análisis automatizado en un pipeline de CD moderno.

Diagnóstico y resolución de problemas reales en Kubernetes (CRDs, etcd, DNS, red).

📄 Licencia
MIT — Libre para usar como referencia o base de estudio.

text

### 🚀 Commit y push

```bash
cd ~/kubernetes-progressive-delivery-lab

cat > README.md << 'EOF'
(pega aquí el contenido de arriba)
EOF

git add README.md
git commit -m "docs: Actualizar README con secciones completas y enlaces a demos"
git push origin main
