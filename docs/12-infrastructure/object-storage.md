# Object Storage Deployment (MinIO / S3)

Configuration of MinIO for local development and AWS S3 for production.

---

## 1. Local Development: MinIO

- Image: `quay.io/minio/minio:latest`.
- Initialization: A bootstrap shell script executes `mc alias set` and creates required buckets (`ecommerce-catalog-media`, `ecommerce-invoices`, `ecommerce-return-proofs`) upon container start.

---

## 2. Production: AWS S3

- Cross-Region Replication (CRR) enabled for disaster recovery.
- CloudFront CDN distribution configured in front of `ecommerce-catalog-media` with edge caching and SSL offload.
- Bucket policy blocks 100% public access to invoices and return proof buckets.
