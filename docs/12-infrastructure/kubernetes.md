# Kubernetes Architecture

Helm charts, Pod manifests, resource limits, and health probes.

---

## 1. Workload Manifest Standards

Each microservice is deployed via Helm chart with:
- **Deployment**: RollingUpdate strategy (`maxSurge: 25%`, `maxUnavailable: 0`).
- **Resource Requests & Limits**:
  - `requests`: `cpu: 250m`, `memory: 512Mi`
  - `limits`: `cpu: 1000m`, `memory: 1024Mi`
- **Liveness Probe**: HTTP GET `/actuator/health/liveness`, port 8080, initialDelay 20s, period 10s.
- **Readiness Probe**: HTTP GET `/actuator/health/readiness`, port 8080, initialDelay 15s, period 5s.
- **Pod Disruption Budget (PDB)**: `minAvailable: 1` ensures at least one replica remains running during cluster node maintenance.
