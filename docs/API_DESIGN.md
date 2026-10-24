# Compliance Shadow — Initial API Design

Base path: `/api/v1`

## 1. API principles

- JSON request/response bodies.
- Stable resource identifiers.
- Pagination for list endpoints.
- Authentication and organisation-level authorization.
- Structured validation errors.
- Findings must include source and evidence references.
- Do not expose LLM or database credentials to the frontend.

## 2. Endpoint catalogue

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/organisations` | Create organisation |
| GET | `/organisations/{organisation_id}` | Retrieve organisation |
| POST | `/sources/sync` | Start permitted source collection |
| GET | `/sources` | List configured sources |
| GET | `/regulatory-documents` | List documents |
| GET | `/regulatory-documents/{document_id}` | Document detail and versions |
| GET | `/regulatory-changes` | Candidate and reviewed changes |
| POST | `/impact-assessments` | Start impact assessment |
| GET | `/findings` | List findings |
| GET | `/findings/{finding_id}` | Finding detail and evidence |
| POST | `/findings/{finding_id}/review` | Approve, reject or amend |
| GET | `/reports/{report_id}` | Retrieve report |

## 3. Example impact assessment request

```json
{
  "organisation_id": "ORG001",
  "regulatory_change_id": "CHANGE001",
  "record_scope": {
    "shipment_ids": ["SHIP001", "SHIP002"]
  }
}
```

## 4. Example finding response

```json
{
  "finding_id": "FIND001",
  "status": "candidate",
  "severity": "medium",
  "confidence": 0.87,
  "summary": "A supporting document may require review under the updated provision.",
  "applicability": {
    "matched_product": "PROD001",
    "reason": "The product category matches the configured rule condition."
  },
  "evidence": [
    {
      "source_url": "https://example.gov.in/notification.pdf",
      "document_version": "v2",
      "page": 4,
      "clause_reference": "3.2"
    }
  ],
  "recommended_action": "Ask a compliance reviewer to verify the document requirement.",
  "requires_human_review": true
}
```

The example URL and identifiers are placeholders, not actual regulatory evidence.

## 5. Review request

```json
{
  "decision": "approve",
  "reviewer_note": "Source clause and applicability checked against the pilot record."
}
```

Allowed decisions should be constrained to the application's review-state model, for example `approve`, `reject` and `amend`.

## 6. Error handling

Return consistent error objects with:
- `error_code`
- `message`
- `request_id`
- optional field-level validation details

Do not return raw prompts, secrets, stack traces or internal database errors to the client.
