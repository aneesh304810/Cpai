# Vendor Technical Review Questions — Zeplyn / Jump PoV

Nov 18, 2026 · Aneesh

## Review summary

The requirements are clear on scope but leave five technical unknowns that decide whether the PoV can run. The questions below target those first.

- **Entitlements are the gating risk.** The critical acceptance requirement (no insight derived from sources a user cannot access) is hardest in Workflow 3, where one answer can blend CAB credit files, PE memos and CRM data.
- **Capture method is undefined.** Phone and in-person meetings are in scope and native iOS capture is out. The vendor must show how each of the four meeting formats is captured.
- **Hosting model is open.** Data residency, retention, storage and deployment model are all listed as "confirm during technical scoping".
- **Integration method for SalesX and Navatar is unknown,** including whether write-back to the system of record is supported and safe.
- **Success criteria have no numbers,** so accuracy cannot yet be accepted or rejected.

About half of the research document applies. Its material on privacy, integration, agent governance, security and observability maps well to the BBH requirements. Its real-time voice material (speech-to-speech models, telephony, interruption handling, response latency) is aimed at a conversational voice agent. BBH needs meeting capture and summarisation, so that material is reduced here to a few capture questions.

The research document's descriptions of Zeplyn (customer-VPC deployment, recording-free capture, PII redaction, zero data retention) come from public marketing pages. Treat them as claims for the vendor to substantiate.

The questions are written for Zeplyn against the BBH PoV. The general sections (deployment, identity, integrations, AI governance, security, operations) can go to Jump unchanged for comparison.

## Must-answer questions

These eight gate the PoV. An unclear answer to any of them should be resolved before detailed scoping.

1. Can the full platform, including model inference, run inside a BBH-controlled tenant or VPC, and which components would still sit outside it?
2. How do you enforce source-system permissions at retrieval time, so a user never receives an answer derived from a document they cannot open?
3. How is each meeting format captured (Teams, Zoom, phone, in-person) without a native iOS app, and is audio ever stored?
4. Which LLM and speech providers process BBH data, and under what retention and no-training terms?
5. Do you have production integrations with SalesX and Navatar today, and do they write as well as read?
6. Can every generated note, proposal element and opportunity be traced to its source record and logged for audit?
7. How are proposals confined to IRG reference portfolios, with no model-invented allocations?
8. What evidence can you provide now: SOC 2 Type II report, penetration test summary, subprocessor list and data-flow diagram?

## Deployment, hosting and data handling

| # | Question | Requirement tested |
| --- | --- | --- |
| D1 | Which deployment options do you offer (multi-tenant SaaS, single-tenant, customer VPC), and which do you propose for the PoV and for production? | Deployment and hosting model |
| D2 | In a customer-VPC deployment, which components run outside it: LLM inference, speech-to-text, telemetry, licensing, vendor support access? | Deployment and hosting model |
| D3 | In which regions is BBH data processed and stored, including model inference and backups? Can all of it be pinned to the US? | Data residency |
| D4 | List every data store the platform creates (transcripts, notes, embeddings, vector indexes, caches, prompts, logs) and the retention period of each. | Storage architecture, retention |
| D5 | Define "zero data retention" precisely. Does it bind you, your LLM providers and your speech providers, and is it contractual? | Data retention |
| D6 | How is deletion executed and evidenced across primary stores, embeddings, backups and subprocessors? What is the timeline at PoV end? | Data deletion |
| D7 | Is BBH data, or anything derived from it, used to train, tune or evaluate models, yours or a third party's? | Permitted data use |
| D8 | Is PII redacted before data reaches an external model? Is it applied to audio, transcript or both, and what is the measured detection rate? | Data classification |
| D9 | How is data encrypted at rest and in transit, and can BBH hold and rotate the keys? | Security review |
| D10 | Do you support private connectivity (private endpoints, VPN, IP allow-listing) with no public endpoints? | Security review |
| D11 | Where real-time access is not needed, how do you ingest controlled snapshots of BBH data, and how is a snapshot refreshed or purged? | Snapshot-based PoV |

## Identity, entitlements and access control

This section tests the critical acceptance requirement directly.

| # | Question | Requirement tested |
| --- | --- | --- |
| A1 | Do you support SSO through SAML 2.0 or OIDC with BBH's identity provider, with MFA enforced there? | SSO |
| A2 | Do you support SCIM provisioning and deprovisioning, and how quickly does a removed user lose access? | User provisioning |
| A3 | Describe your authorization model: role-based, attribute-based, record-level or document-level. | Role-based access |
| A4 | How are source-system permissions (SharePoint ACLs, SalesX and Navatar record sharing, mailbox access) reflected in Zeplyn: synced copies or live checks at query time? | Authorized access only |
| A5 | If access is revoked in a source system, how long until Zeplyn stops returning that content? | Authorized access only |
| A6 | Are permissions applied before retrieval, so the model never sees content the user cannot access, or filtered after generation? | Critical acceptance requirement |
| A7 | How do you prevent leakage through derived artefacts: summaries, embeddings, cached answers, firm-wide insights and scheduled reports sent to groups? | Critical acceptance requirement |
| A8 | How do you enforce information barriers, for example keeping CAB credit files and PE investment memos from users outside those groups? | Critical acceptance requirement |
| A9 | Can BBH exclude whole data classifications or repositories from AI processing? | Permitted data classifications |
| A10 | What access do Zeplyn staff have to BBH data, and how is it approved, time-limited and logged? | Security review |
| A11 | How will you help BBH prove the acceptance requirement, for example negative tests with users of differing entitlements? | Access-control success metric |

## Workflow 1: Meeting Assistant

| # | Question | Requirement tested |
| --- | --- | --- |
| M1 | For each format (Teams, Zoom, phone, in-person), what is the capture mechanism: bot participant, native transcript API, desktop app, dial-in or upload? | Four meeting formats |
| M2 | With native iOS capture out of scope, how are in-person meetings and mobile phone calls captured? | Phone and in-person |
| M3 | Can you consume native Teams and Zoom transcripts instead of joining a bot? Which Microsoft Graph permissions and Zoom scopes are required? | Teams/Zoom ingestion |
| M4 | Is audio recorded, streamed or processed in memory only? If nothing is recorded, what is kept to verify a disputed note? | Retention, auditability |
| M5 | How are participants notified and consent captured, including external attendees and all-party-consent jurisdictions? | Legal and Compliance review |
| M6 | How are meetings selected for capture by type, and how are internal or privileged meetings excluded? | Selected meeting types |
| M7 | For each required field (client and firm, relationship type, owner, attendees, date and time, format, purpose, notes, action items, action owners), is it extracted from the conversation or pulled from calendar and CRM? | Minimum capture fields |
| M8 | How do you match a meeting to the correct SalesX or Navatar record, and what happens on an ambiguous or missing match? | System-of-record update |
| M9 | What is your measured accuracy on names, numbers, financial terms and speaker attribution with three or more speakers? Can BBH add its own vocabulary? | Extraction accuracy |
| M10 | How are low-confidence items flagged, and what review step occurs before anything is written to the system of record? | Accuracy, system-of-record update |
| M11 | How are notes routed to distribution lists and shared mailboxes, and how is each recipient's entitlement checked? | Distribution lists, authorized access |
| M12 | Can KYC/AML and advisor-coaching features be switched off at tenant level for the PoV? | Out-of-scope items |

## Workflow 2: Investment Proposal Generator

| # | Question | Requirement tested |
| --- | --- | --- |
| P1 | How is each input ingested (goals, risk profile, current portfolio, previous decisions, liquidity, tax preferences including K-1, constraints, CPMS questionnaire and IPS), and which must be structured? | Proposal inputs |
| P2 | How are IRG reference portfolios represented, versioned and kept current, and who at BBH maintains them? | Reference portfolio logic |
| P3 | Is the portfolio recommendation produced by deterministic rules, by the LLM or by a mix? Where exactly does the model exercise judgement? | Traceability of recommendations |
| P4 | Can output be restricted to approved reference portfolios and products, with no model-generated allocations or securities? | Accuracy of recommendations |
| P5 | How are hard constraints such as liquidity needs, investment restrictions and K-1 preferences enforced and shown in the output? | Proposal inputs |
| P6 | Does every statement and figure in the proposal link back to its source: a CRM field, a meeting note or a questionnaire answer? | Traceability |
| P7 | How do you handle conflicting or stale inputs, for example a meeting note and a questionnaire that give different risk tolerances? | Accuracy of client information |
| P8 | Are figures calculated by code or generated by the model, and how are they validated? | Accuracy |
| P9 | What is the RM review workflow: edit, approve, reject, version history, and a record of what the AI drafted versus what the RM changed? | RM review, auditability |
| P10 | Can proposals be produced in BBH templates with required disclosures inserted automatically? | Initial proposal for RM review |
| P11 | With Pivotal integration out of scope, which proposal inputs will be missing or loaded manually? | PoV scope |
| P12 | How will you baseline current preparation time and measure the reduction? | Prep-time success metric |

## Workflow 3: Opportunity Identification

| # | Question | Requirement tested |
| --- | --- | --- |
| O1 | For each source (SalesX, Navatar, executive ecosystem list, historical investment catalog, distribution lists, CAB credit files, PE investment memos, CAG documents and decks, industry reports, event and conference attendee lists), what connector and ingestion method do you use? | Information sources |
| O2 | What is the retrieval architecture: vector search, knowledge graph or both? How are relationships between people, companies and deals modelled? | Relationship discovery |
| O3 | How do you resolve the same person or company across CRM records, memos and attendee lists, and what is the measured match precision? | Quality of connections |
| O4 | How are scanned PDFs, slide decks and spreadsheets parsed, including tables and images? | Unstructured data |
| O5 | Does every surfaced connection show its evidence (source document, passage, date) and the reason it is relevant? | Relevance, auditability |
| O6 | How is relevance ranked, can BBH tune it, and how do you measure the quality of the top results? | Quality of connections |
| O7 | For scheduled discovery, how are jobs defined, whose entitlements do they run under, and how are results distributed? | Scheduled discovery, access control |
| O8 | How is access enforced when one insight combines sources with different permissions, such as a credit file and a CRM note? | Critical acceptance requirement |
| O9 | How quickly do new, changed and deleted source documents take effect in the index? | Authoritative data sources |
| O10 | CapIQ, DebtDomain and other third-party sources are still to be decided. Do you have connectors, and how are licence limits on redistribution respected? | Third-party sources |
| O11 | Can users rate a connection and record that it was acted upon, so BBH can report the success metrics? | Opportunities identified and acted upon |
| O12 | Which subset of sources do you recommend for the PoV, and what discovery and integration effort does each need? | Discovery and integration effort |

## Integrations

| # | Question | Requirement tested |
| --- | --- | --- |
| I1 | Do you have a production integration with SalesX today? Which interface does it use, and which objects does it read and write? | SalesX integration method |
| I2 | Do you have a production integration with Navatar, and does it use the underlying CRM platform's APIs or a Navatar-specific interface? | Navatar integration method |
| I3 | Does each integration authenticate as the individual user or through a service account? If a service account, how is user-level access preserved? | Authentication and authorization |
| I4 | Which Microsoft Graph permissions does SharePoint access need, can it be limited to selected sites, and does it honour item-level permissions and sensitivity labels? | SharePoint access |
| I5 | Which mailbox scopes does Outlook connectivity need, and can it be limited to named shared mailboxes? | Outlook/email connectivity |
| I6 | For write-back, which fields are created or updated, is human approval required, are writes idempotent, and how do you avoid overwriting good data? | System-of-record update |
| I7 | How do you handle API rate limits, source outages and partial failures: retries, failed-update queue and reconciliation? | Integration reliability |
| I8 | Can BBH map custom objects and fields without vendor engineering? | Integration method |
| I9 | Do you expose APIs, webhooks or exports so BBH can pull notes, proposals and audit data into its own data platform? | Auditability, data access |
| I10 | Does any integration rely on browser automation or screen scraping? | Integration method |
| I11 | What must BBH build or expose for the PoV: APIs, extracts, service accounts, firewall rules? | Sandbox and technical access |

## AI models, accuracy and governance

| # | Question | Requirement tested |
| --- | --- | --- |
| G1 | Provide a model inventory per workflow: provider, model, version and hosting location for speech, reasoning and embeddings. | AI governance controls |
| G2 | Can BBH choose or restrict models, or supply its own model endpoint inside a BBH cloud tenant? | Deployment model, data residency |
| G3 | How are model version changes tested and announced, and can BBH pin versions for the PoV? | Accuracy |
| G4 | How do you evaluate accuracy and hallucination, can you share results on wealth-management data, and will you run a BBH test set? | Accuracy success metrics |
| G5 | How are outputs grounded: retrieval only, mandatory citations, and refusal when no source supports an answer? | Accuracy, traceability |
| G6 | Which agents act autonomously and which need approval? What permissions does each hold, and can BBH disable any of them? | AI governance controls |
| G7 | How do you defend against prompt injection from emails, documents or transcripts that contain instructions to the agent? | Information Security review |
| G8 | Does the audit log capture user, prompt, retrieved sources, model and version, output and actions taken? Is it immutable, and can it export to BBH's SIEM? | Auditability of AI access and outputs |
| G9 | What documentation supports BBH's AI use-case and model-risk review: model descriptions, known limitations, test evidence? | AI-use-case committee review |
| G10 | What controls stop the model from producing investment advice or content outside BBH-approved material? | Compliance review |

## Security, compliance and vendor due diligence

| # | Question | Requirement tested |
| --- | --- | --- |
| S1 | For your SOC 2 Type II report, what are the scope, period, exceptions and customer-side controls? Does it cover the proposed deployment model and all AI components? | Vendor due diligence / VTR |
| S2 | When was the last independent penetration test, did it cover the AI attack surface, and are all critical and high findings closed? | Information Security review |
| S3 | List every subprocessor, the BBH data each receives, and your notice period for changes. | Vendor due diligence / VTR |
| S4 | What is your contractual incident-notification window, and does it support BBH's 72-hour reporting obligation under NYDFS Part 500? | Risk and Compliance review |
| S5 | Can you attest to the controls BBH must require of third parties under NYDFS Part 500: access controls, MFA, encryption and notice of security events? | Risk and Compliance review |
| S6 | How can BBH retain AI-generated notes and client-related outputs in its own archive to meet record-retention obligations? | Data retention requirements |
| S7 | How do you support legal hold and e-discovery on transcripts, notes and AI outputs? | Legal review |
| S8 | In a multi-tenant deployment, how are tenants isolated across data, indexes, keys and model context? | Information Security review |
| S9 | Describe your secure development practice: vulnerability-fix timelines, dependency scanning and software bill of materials. | Information Security review |
| S10 | What are your recovery time and recovery point objectives, and when was disaster recovery last tested? | Vendor due diligence / VTR |
| S11 | Describe company funding and runway, cyber and professional-liability insurance, and the data-return plan if you cease operations. | Vendor due diligence / VTR |
| S12 | Which regulated banks or wealth managers of similar size can serve as references, especially for the proposed deployment model? | Vendor due diligence / VTR |

## Reliability, operations and observability

| # | Question | Requirement tested |
| --- | --- | --- |
| R1 | What availability commitments, support hours and severity response times do you offer? | Vendor due diligence / VTR |
| R2 | What happens when an LLM or speech provider is unavailable: failover, queueing, or lost meetings? | Meetings-captured metric |
| R3 | What volumes do you support (concurrent meetings, indexed documents), and what is query response time at median and 95th percentile for Workflow 3? | User adoption |
| R4 | Can one item be traced end to end, from capture through model call to CRM write, and can BBH see those traces? | Auditability |
| R5 | How do you monitor output quality in production: accuracy drift, user corrections and reported errors? | Accuracy |
| R6 | What is your release cadence and change-notice period, and do you provide a separate non-production environment for BBH? | Sandbox and technical access |
| R7 | In a customer-VPC deployment, who operates, patches and upgrades the platform, and what infrastructure and sizing does it need? | Deployment and hosting model |
| R8 | Can usage and cost be reported per workflow, team and user? | Adoption metrics |
| R9 | Can a BBH administrator immediately disable a workflow, agent or integration? | AI governance controls |

## PoV delivery, success metrics and commercial

| # | Question | Requirement tested |
| --- | --- | --- |
| V1 | Propose a numeric target and measurement method for each success criterion: meetings captured, field completeness, extraction accuracy, prep-time reduction, connection relevance, and weekly active users against total pilot users. | Agreed success criteria |
| V2 | Who builds the ground truth for accuracy scoring, and what sample size do you recommend? | Accuracy metrics |
| V3 | What timeline and sequence do you propose for the three workflows, and what can be delivered before VTR and Information Security review complete? | Dependencies |
| V4 | Can the PoV start on synthetic or masked data in a sandbox, and do you provide that sandbox? | Sandbox and technical access |
| V5 | Who are the named engineers, where are they located, are they background-checked, and what BBH data will they see? | Dedicated engineering |
| V6 | What do you need from BBH, and by when: data, system access, SMEs and decisions? | Timely access to data and systems |
| V7 | How is the product instrumented to report adoption and workflow usage? | Adoption metrics |
| V8 | What changes between PoV and production in architecture, contract and pricing? Who pays model usage costs? | Pilot/consulting agreement |
| V9 | Who owns outputs, prompts, configurations and any BBH-specific tuning? Is anything learned at BBH reused for other clients? | Pilot/consulting agreement |
| V10 | How is BBH data returned and deletion certified at PoV end? | Data retention and deletion |

## Evidence to request

Ask for documents alongside the answers, so claims can be checked.

| Document | What it lets BBH verify |
| --- | --- |
| Architecture diagram of the proposed BBH deployment | Which components run where |
| Data-flow diagram per workflow, showing every external call | Where BBH data travels |
| Network architecture | Connectivity and isolation |
| Entitlement design document | How source permissions are enforced at retrieval |
| AI model inventory | Providers, versions and hosting |
| Subprocessor register | Third-party exposure |
| Data retention and deletion policy | Storage periods and deletion proof |
| SOC 2 Type II report with bridge letter | Control scope and exceptions |
| Penetration test summary | Security testing and open findings |
| Disaster recovery plan and last test result | Resilience |
| API and integration documentation, including required Microsoft Graph and Zoom permissions | Integration method and access footprint |
| AI governance policy and evaluation results | Model risk and accuracy evidence |
| Sample audit log export | Whether AI access and outputs can be reconstructed |
| Completed standard security questionnaire | Input to VTR |
