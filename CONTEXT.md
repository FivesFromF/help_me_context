# Project Context: HelpMe – Emergency Lifeline

**Team:** FivesFromF
**Project name:** HelpMe

---

## 1. Vision

HelpMe aims to build a centralized platform that detects people in distress quickly and accurately, emphasizing the use of multiple complementary methods. From there, citizens and healthcare workers can access a patient's medical history and health profile (allergies, blood type, etc.) along with next-of-kin information, enabling the earliest possible response within the "golden hour".

---

## 2. Technical Stack

### 2.1 Backend & Infrastructure (Cloud Native)
- **Core Engine:** Go (v1.24+) running on AWS ECS.
- **Communication:** Idiomatic RESTful API (net/http) & EventBridge for event-driven communication.
- **Primary DB:** PostgreSQL (RDS) for business data.
- **Identity:** AWS Cognito with federated Google identity & Cognito Groups.
- **Logging System (Audit Trail):** Amazon DynamoDB as the centralized audit trail.
- **Integration:** SMTP (Email) for emergency next-of-kin notifications (replaced SMS/Twilio).

> **Note:** The implemented backend in `help_me_backend` uses **TypeScript AWS Lambdas** and a **Python FastAPI** AI service rather than the Go/ECS design described above. See the root `CLAUDE.md`.

---

## 3. AI Processing (Core Logic)

The AI system performs two main tasks:
1. **Security (Liveness Detection):** Prevents impersonation using Silent-Face-Anti-Spoofing.
2. **Identification:** Extracts features (ArcFace) and compares cosine similarity to determine identity.

---

## 4. Data Retrieval Methods

1. **Face Recognition:** AI identity capture (priority #1).
2. **NFC Tag:** Scan a medical bracelet/keychain (for cases of lost consciousness where AI struggles to recognize).
3. **QR Code:** Fallback option printed on helmets/vehicles.

---

## 5. Access Control & Security

Complies with Decree 13/2023/NĐ-CP on the protection of sensitive personal data.

| Role | Provisioning | Permissions |
| :--- | :--- | :--- |
| **Citizen** | Google Sign-In | Automatically assigned to the `citizen` group. Registers 5 face angles, NFC, QR, and a basic medical profile. |
| **Healthcare Staff** | Admin provisioned | Belongs to the `staff` group. Manages rescue cases and accesses detailed medical profiles. |
| **Admin** | Admin provisioned | Belongs to the `admin` group. Manages staff, views system statistics and audit logs. |

### 5.1 Data Structure (3-Table Schema)
To optimize security and performance, the system separates three main tables: `citizens`, `staff`, `admins`.
- **Citizens**: Stores biometrics (face vector) and personal identity information (CCCD / national ID).
- **Staff**: Stores workplace information (hospital, department).
- **Admins**: Stores administrative privileges.
