# CI/CD Pipeline Architecture

GitHub Actions automated build, test, containerization, and deployment stages.

---

## 1. Automated Pipeline Stages

```text
[Pull Request Created]
         |
         v
+-------------------------------------------------------+
| Stage 1: Static Analysis (Checkstyle, SonarQube)       |
+-------------------------------------------------------+
         |
         v
+-------------------------------------------------------+
| Stage 2: Unit Tests (JaCoCo Coverage >= 85%)          |
+-------------------------------------------------------+
         |
         v
+-------------------------------------------------------+
| Stage 3: Integration Tests (Testcontainers PG/Kafka)  |
+-------------------------------------------------------+
         |
         v
+-------------------------------------------------------+
| Stage 4: Container Build & Trivy Vulnerability Scan   |
+-------------------------------------------------------+
         | (Merge to Main)
         v
+-------------------------------------------------------+
| Stage 5: Deploy to Staging (Kubernetes Rolling Update)|
+-------------------------------------------------------+
         | (Automated E2E Playwright Smoke Tests Pass)
         v
+-------------------------------------------------------+
| Stage 6: Canary Deploy to Production (10% -> 100%)    |
+-------------------------------------------------------+
```
All stages are fully automated with zero manual gates on PR validation.
