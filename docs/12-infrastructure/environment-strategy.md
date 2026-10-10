# Environment Strategy

Staging, development, and production environments, promotion criteria, and isolation.

---

## 1. Environments

1. **Local (`dev`)**: Developer laptop running Docker Compose backing services. Rapid feedback loop.
2. **CI / Test (`test`)**: Transient ephemeral test runner spun up per GitHub Action workflow using Testcontainers.
3. **Staging (`stage`)**: Full Kubernetes cluster mirroring production topology and sizing. Ingests synthetic load and runs daily automated E2E suites.
4. **Production (`prod`)**: Multi-AZ Kubernetes cluster and managed databases. Zero experimental deployments; immutable tagged container images promoted directly from staging.
