# Object Storage Design

Bucket structures, access policies, pre-signed URLs, and lifecycle management for S3 / MinIO.

---

## 1. Bucket Organization & Access Control

| Bucket Name | Access Policy | Content Type | Retention Policy |
| :--- | :--- | :--- | :--- |
| `ecommerce-catalog-media` | Public Read (via CDN) | Product photos, WebP thumbnails, videos | Indefinite (Versioned) |
| `ecommerce-invoices` | Private (Auth required) | Generated customer GST tax invoice PDFs | 8 Years (Statutory) |
| `ecommerce-return-proofs` | Private (Auth required) | Customer return photos / defect proofs | 1 Year |
| `ecommerce-export-reports`| Private (Admin only) | Generated financial & analytics CSV exports| 30 Days (Auto-expire) |

---

## 2. Pre-Signed Upload URL Pattern

To avoid proxying heavy binary files through Spring Boot microservice heaps:
1. Client requests an upload slot: `POST /api/v1/media/upload-url { "fileName": "photo.jpg", "contentType": "image/jpeg" }`.
2. Service generates an AWS S3 pre-signed PUT URL with a 15-minute expiration.
3. Client uploads directly to S3 / MinIO via HTTP PUT.
4. Client notifies the service with the uploaded object key for background image processing (WebP conversion and thumbnailing).
