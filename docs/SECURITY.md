# Compliance Shadow — Security and Responsible AI

## 1. Security objectives

Protect business records, preserve regulatory evidence integrity and prevent AI-generated content from triggering unauthorized actions.

## 2. Core controls

| Area | Control |
|---|---|
| Authentication | Use a supported identity provider and secure sessions/tokens |
| Authorization | Enforce organisation-level access and role-based permissions |
| Secrets | Keep credentials in secret management or protected environment variables |
| Transport | Use HTTPS/TLS for service and database connections |
| Data access | Apply least privilege and parameterized queries |
| Uploads | Validate file type, size and content; scan where feasible |
| Audit | Record source versions, model runs, review decisions and timestamps |
| Retention | Define retention and deletion rules for uploaded and business data |
| Backups | Protect backups and test restoration |
| Dependencies | Scan dependencies and keep a patch process |

## 3. AI-specific controls

- Treat regulatory PDFs, web pages and retrieved text as untrusted input.
- Do not follow instructions embedded in source documents.
- Restrict agent tools to an explicit allowlist.
- Validate model output against typed schemas and deterministic checks.
- Require citations for material regulatory claims.
- Prevent agents from executing external submissions or consequential business changes.
- Escalate low-confidence, conflicting or ambiguous cases to human review.
- Log model/provider identifiers, retrieved evidence and tool execution metadata.
- Evaluate prompt-injection resistance and unsupported-claim rates.

## 4. Privacy and test data

Use synthetic records for the academic prototype. If real organisation data is introduced, obtain authorization, minimize fields, define access controls and agree retention/deletion practices.

## 5. Responsible-use statement

Compliance Shadow is a decision-support tool. It does not guarantee legal compliance, replace professional advice or certify that a business is compliant. The platform communicates potential impacts and evidence for review by an authorized person.
