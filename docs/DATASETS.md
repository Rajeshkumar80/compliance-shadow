# Compliance Shadow — Dataset and Data Acquisition Plan

## 1. Dataset strategy

The project needs three distinct data groups:

1. **Regulatory source documents** — official publications used for extraction and evidence.
2. **Labelled document-understanding data** — selected pages/clauses annotated for the deep-learning task.
3. **Business impact data** — synthetic or permissioned organisation, product, shipment and document records.

Do not mix these groups without recording provenance and permitted use.

## 2. Candidate regulatory sources

| Source | Candidate content | Pilot priority |
|---|---|---|
| DGFT — https://www.dgft.gov.in/ | Trade notices, notifications and export/import policy documents | High |
| CBIC — https://www.cbic.gov.in/ | Customs and indirect-tax circulars/notifications | High |
| RBI — https://www.rbi.org.in/ | Relevant foreign-exchange and payment directions | Later |
| FSSAI — https://www.fssai.gov.in/ | Food-related requirements, if selected sector needs them | Sector-dependent |
| BIS — https://www.bis.gov.in/ | Standards/conformity information, subject to access and reuse terms | Sector-dependent |
| MCA — https://www.mca.gov.in/ | Corporate compliance circulars and notifications | Later |

The MVP should select one domain and a manageable, verified document set. Do not promise coverage of every authority at launch.

## 3. Supporting software datasets (optional extension)

| Source | Use |
|---|---|
| OSV — https://osv.dev/ | Open-source dependency vulnerability records |
| NIST NVD — https://nvd.nist.gov/ | CVE and vulnerability metadata |
| SPDX — https://spdx.org/licenses/ | Standardized open-source licence identifiers |
| GitHub REST API docs — https://docs.github.com/en/rest | Public repository metadata and permitted repository access |

These datasets support a possible future repository/software compliance module. They are not required for the initial regulatory-change MVP.

## 4. Synthetic business dataset

Create synthetic records for:
- Organisation and business sector
- Product category and attributes
- Destination country/market
- Shipment date and shipment status
- Invoice, certificate and supporting-document status
- Relevant threshold or exemption attributes

Example CSV columns:

```csv
organisation_id,product_id,product_category,destination,shipment_id,shipment_date,document_type,document_status
ORG001,PROD001,SampleCategory,MarketA,SHIP001,2026-09-10,CertificateOfOrigin,Available
ORG001,PROD001,SampleCategory,MarketA,SHIP002,2026-10-05,CertificateOfOrigin,Pending
```

All sample values are fictional. Do not represent synthetic data as actual customer records.

## 5. Deep-learning annotation schema

For each annotated clause, capture:

| Field | Description |
|---|---|
| document_id | Stable source document identifier |
| version_id | Specific document version |
| page_number | One-based page index |
| clause_reference | Printed clause/paragraph identifier, if present |
| text_span | Exact text span for the annotation |
| rule_type | Obligation, prohibition, threshold, exemption, definition, effective-date clause, etc. |
| authority | Issuing authority |
| publication_date | Date published |
| effective_date | Date the provision takes effect, if stated |
| product_or_activity | Mentioned product/activity/category |
| conditions | Structured conditions or exceptions |
| reviewer_id | Annotator/reviewer identifier |

Use a documented annotation guide and double-review a subset to estimate label consistency.

## 6. Train/validation/test split

- Split by document or publication family, not random text chunks alone.
- Keep near-duplicate versions together where possible to avoid leakage.
- Maintain a fixed holdout test set.
- Record dataset version and annotation-guide version.
- Track source, collection date, hash and reuse constraints.

## 7. Quality and legal-use safeguards

- Confirm source terms and access policies before crawling.
- Respect rate limits and avoid bypassing access controls.
- Keep the original publication and extraction output linked.
- Mark OCR uncertainty and missing pages.
- Never infer an effective date when the source does not state one.
- Have a knowledgeable reviewer validate training labels and high-impact findings.
