# SMS Channel Specification

Provider adapters, regulatory DLT compliance in India, and character limits.

---

## 1. Provider Adapter & Regulatory Standards

- **Provider**: Twilio with regional failover adapter to Gupshup.
- **Regulatory DLT Compliance (TRAI - India)**:
  - All transactional SMS templates are pre-registered on the telecom Distributed Ledger Technology (DLT) portal with registered Header IDs and Entity IDs.
  - SMS messages must match the exact pre-approved DLT format; unregistered variations are rejected by telecom carriers at the edge.
- **Character Encoding**: GSM 7-bit (160 characters per segment). Concatenated SMS (CSMS) used for messages exceeding 160 characters.
