# 📘 Database & OS Security Overview

## 🔹 1. Introduction to Data & Database Security

### Concept & Purpose
Databases are critical assets storing essential organizational data. Securing them ensures:
- Protection against unauthorized access and misuse.
- Compliance with industry standards.
- Preservation of confidentiality, integrity, and availability (CIA).

### Security Measures
- Access control
- Inference control
- Encryption
- Authentication
- Integrity control
- Backups
- Application security

---

## 🔍 Database Security Threats

| Threat Type               | Description |
|---------------------------|-------------|
| Excessive Privileges      | Users granted more rights than necessary |
| Legitimate Privilege Abuse| Authorized users misuse their privileges |
| Injection Attacks         | SQL/code injection |
| Malware                   | Malicious software targeting DB systems |
| Storage Media Exposure    | Data leaks via lost/unprotected media |
| Vulnerable Databases      | Outdated/unpatched software |
| Unmanaged Sensitive Data  | Poorly classified/unsecured data |
| Human Factor              | Insider threats, mistakes, negligence |

---

## 🔐 Security Control Types

| Type        | Purpose / Description | Techniques / Examples |
|------------|----------------------|---------------------|
| 🟢 Preventive | Stop attacks before they happen | Least privilege, firewalls, ACLs |
| 🟡 Detective | Identify and record incidents | IDS, log monitoring, auditing |
| 🔵 Corrective | Recover systems/data | Backup/restore, patching, disaster recovery |
| 🔴 Deterrent | Discourage malicious actions | CCTV, warnings, policies, legal actions |

---

## 🕵️‍♀️ Threat Modeling

| Term              | Meaning |
|------------------|---------|
| Asset             | Anything valuable (data, system) |
| Threat            | Undesired act harming an asset |
| Threat Agent      | Entity causing the threat |
| Vulnerability     | Weakness exploitable by threats |
| Attack (Exploit)  | Malicious act by threat agent |
| Safeguard/Control | Countermeasure reducing risk |
| Probability       | Chance of attack occurring |
| Impact            | Damage if threat materializes |

### Types of Threat Agents
- Accidental Discovery: User mistakenly finds sensitive info
- Curious Attacker: User intentionally explores weaknesses
- Insider: Employee/contractor misusing access

---

## 🚨 Access Control & Data Security

| Model      | Description |
|------------|-------------|
| DAC        | Owner controls access (ACLs) |
| MAC        | System-enforced labels/classifications |
| RBAC       | Role-based access control |
| RuBAC      | Rule/condition-based access control |

### MAC Models

| Model           | Focus        | Key Rules / Notes |
|----------------|-------------|-----------------|
| Bell-LaPadula  | Confidentiality | No read up, no write down |
| Biba           | Integrity   | No read down, no write up |
| Clark-Wilson   | Integrity   | Well-formed transactions, separation of duties |
| Brewer-Nash    | Confidentiality/Integrity | Dynamic access, conflict-of-interest prevention |

---

## 🔸 Integrity Principles & ACID

| Property  | Description |
|-----------|-------------|
| Atomicity | All-or-nothing transactions |
| Consistency | DB moves from one valid state to another |
| Isolation | Changes invisible until commit |
| Durability | Committed changes are permanent |

Other principles: Entity Integrity, Referential Integrity, Least Privilege, Constraints

---

## 🩹 Views & Updatable Views

- Virtual representation of data
- Control visibility of sensitive data
- Options: `OR REPLACE`, `FORCE/NOFORCE`, `WITH READ ONLY`, `WITH CHECK OPTION`

---

## 🔐 Encryption & TDE

- **Transparent Data Encryption (TDE):** Protects data at rest, meets PCI DSS
- Algorithms: AES, 3DES
- Encrypts columns or full tablespaces automatically
- Protects against media theft without app modification

---

## 🗑️ Data Destruction

- **Erasing:** Deletes pointers only
- **Cleaning:** Overwrites with unclassified data
- **Purging:** Repeated cleaning + degaussing
- **Destruction:** Physical shredding/incineration

---

## 🧩 Data Masking

- Obfuscates sensitive data for testing/training
- Techniques: Substitution, Redaction, Shuffling, Blurring, De-Identification
- Preserves format, type, referential integrity, uniqueness

---

## 🔐 Password & Account Management

- Lock default accounts (SYS, SYSTEM)
- Enforce password complexity & aging
- Case-sensitive passwords (from Oracle 11g+)
- User-specific restrictions & auditing

---

## 🛡️ Auditing

- Monitors access, tracks modifications, ensures accountability
- Types: Standard, Fine-Grained (FGA)
- Roles: `AUDIT_ADMIN`, `AUDIT_VIEWER`
- Protect audit trail, archive logs, regular review

---

## 🧬 VPD (Virtual Private Database)

- Row-level security based on user identity/context
- Centralized enforcement
- Example: `DBMS_RLS.ADD_POLICY`

---

## 🧰 Data Encryption Example (DBMS_CRYPTO)

```sql
ALTER TABLE Customer ADD (NIC_ENC RAW(2000));
BEGIN
  FOR r IN (SELECT customer_ID, NIC FROM Customer) LOOP
    UPDATE Customer
    SET NIC_ENC = DBMS_CRYPTO.ENCRYPT(
      UTL_RAW.CAST_TO_RAW(r.NIC),
      DBMS_CRYPTO.DES_CBC_PKCS5,
      UTL_RAW.CAST_TO_RAW('DOSS2025')
    )
    WHERE customer_ID = r.customer_ID;
  END LOOP;
  COMMIT;
END;
/
