

| Category                                    | Quantity & Assumptions                                  | Unit Cost (USD)                      | Monthly Cost (USD)                 |
| ------------------------------------------- | ------------------------------------------------------- | ------------------------------------ | ---------------------------------- |
| **Cloud Firestore reads**                   | 20 000 DAU × 50 reads/day × 30 days = 30 000 000 reads  | \$0.036 per 100 000 reads            | (30 M/100 000)×0.036 = **\$10.80** |
| **Cloud Firestore writes**                  | 20 000 DAU × 10 writes/day × 30 days = 6 000 000 writes | \$0.109 per 100 000 writes           | (6 M/100 000)×0.109 = **\$6.54**   |
| **Stored data (Firestore)**                 | \~2 GiB total (profiles, rides, indexes)                | \$0.182 per GiB-month                | 2×0.182 = **\$0.36**               |
| **Firebase Authentication (SMS OTP)**       | 100 000 unique verifications × \$0.01/SMS               | \$0.01 per SMS                       | **\$1 000**                        |
| **Firebase Storage (images)**               | 20 GB compressed assets                                 | \$0.026 per GB-month                 | 20×0.026 = **\$0.52**              |
| **CDN egress (via Firebase or Cloudflare)** | \~10 GB outgoing                                        | \~\$0.15 per GB                      | 10×0.15 = **\$1.50**               |
| **Cloud Functions (on-demand only)**        | ≪ free tier (invocations < 2 M)                         | \$0.40 per extra million invocations | **\$0.00** (covered in free tier)  |

> **Total ≈ \$1 019 / month**

