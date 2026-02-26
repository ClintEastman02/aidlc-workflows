# Extension Generator Skill

**Skill**: extension-generator v3.0.0 | **Stage**: Workflow Planning (one-time) | **IDE-Agnostic**

## Purpose

Generates AI-DLC extension folders for any ruleset — compliance frameworks, security standards, coding conventions, business rules, team processes, or anything else. For security/compliance/privacy rules, uses CSA CCM v4.1 as the normalization layer. For non-compliance rules, generates directly without CCM overhead.

---

## Rule Type Classification

Before doing anything else, classify what the user is asking for:

| Rule Type | Category | CCM Applies? | Examples |
|-----------|----------|-------------|----------|
| Security/Compliance/Privacy | `security`, `compliance` | **Yes** — full CCM resolution | HIPAA, PCI-DSS, GDPR, SOC 2, NIST, ISO 27001, OWASP |
| Coding Standards | `quality` | **No** | Style guides, linting rules, naming conventions, language idioms |
| Business Rules | `process` | **No** | Approval workflows, SLA requirements, domain logic constraints |
| Architecture Standards | `architecture` | **No** (unless security-related) | API design patterns, microservice boundaries, data modeling rules |
| Team/Org Process | `process` | **No** | PR review rules, branching strategy, documentation requirements |
| Mixed | Multiple extensions | **Split** — CCM for compliance parts, direct for the rest | "Our HIPAA rules plus our coding standards" |

**Detection heuristic**: If the user's input mentions a known framework from the Source-of-Truth Registry, or contains keywords like encrypt, audit, access control, compliance, regulation, privacy, breach, vulnerability — classify as security/compliance. Otherwise, classify as non-compliance.

For **mixed** inputs: split into separate extensions by type. Don't force coding conventions through CCM.

---

## CCM-Anchored Resolution (security/compliance/privacy only)

For rules classified as security/compliance/privacy, follow this deterministic order instead of random web searches:

```
1. CCM Domain Lookup        → relevant CCM v4.1 domain(s) + control IDs
2. Official Framework Mapping → CCM ↔ framework crosswalk (ISO, NIST, PCI, etc.)
3. Official Source of Truth   → issuing body URL for legal/regulatory text
4. Cloud Provider Artifacts   → provider's own CCM/STAR alignment docs
5. Gap Fill (last resort)     → web search only for what CCM doesn't cover
```

**Why**: CCM v4.1 has 207 controls across 17 domains, harmonizes 40+ global standards, and every major cloud provider publishes CCM/STAR alignment. Extensions become cross-framework comparable.

For **non-compliance rules**: skip this entire section. Go straight to Step 1 → Step 3 → Step 4.

---

## CCM v4.1 Domain Reference

| ID | Domain | Scope | Controls |
|----|--------|-------|----------|
| AIS | Application & Interface Security | Secure coding, API security, runtime protections | AIS-01–07 |
| A&A | Audit & Assurance | Audits, evidence collection, control verification | A&A-01–06 |
| BCR | Business Continuity & Resilience | Backup, DR, continuity planning | BCR-01–11 |
| CCC | Change Control & Config Mgmt | Config baselines, change processes, IaC | CCC-01–09 |
| DSP | Data Security & Privacy Lifecycle | Classification, encryption, retention, destruction | DSP-01–19 |
| DCS | Datacenter Security | Physical/environmental safeguards | DCS-01–09 |
| CEK | Cryptography, Encryption & Key Mgmt | Crypto standards, key lifecycle, certs | CEK-01–21 |
| GRC | Governance, Risk & Compliance | Legal, regulatory, contractual alignment | GRC-01–08 |
| HRS | Human Resources Security | Screening, acceptable use, awareness | HRS-01–13 |
| IAM | Identity & Access Management | Least privilege, MFA, lifecycle, service accounts | IAM-01–16 |
| IVS | Infrastructure & Virtualization | Hypervisor, container, serverless hardening | IVS-01–13 |
| IPY | Interoperability & Portability | Open standards, data export, lock-in prevention | IPY-01–04 |
| LOG | Logging & Monitoring | Collection, correlation, retention, alerting | LOG-01–13 |
| SEF | Security Incident Mgmt & Forensics | Incident response, evidence, legal discovery | SEF-01–08 |
| STA | Supply Chain & Accountability | Third-party oversight, SBOM, contracts | STA-01–14 |
| TVM | Threat & Vulnerability Mgmt | Vuln ID, threat intel, pen testing, remediation | TVM-01–12 |
| UEM | Universal Endpoint Management | Endpoint security, BYOD, device compliance | UEM-01–15 |


## Official Source-of-Truth Registry

Always cite the issuing body — never a blog or third-party summary.

| Framework | Issuing Body | URL | CCM Domains |
|-----------|-------------|-----|-------------|
| HIPAA | U.S. HHS | https://www.hhs.gov/hipaa/ | DSP, CEK, IAM, LOG, SEF |
| PCI-DSS | PCI SSC | https://www.pcisecuritystandards.org/ | DSP, CEK, IAM, LOG, AIS |
| GDPR | EU | https://eur-lex.europa.eu/eli/reg/2016/679/oj | DSP, GRC, IAM, LOG |
| SOC 2 | AICPA | https://www.aicpa.org/trustservicescriteria | Official CCM mapping |
| NIST 800-53r5 | NIST | https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final | Official CCM mapping |
| NIST CSF 2.0 | NIST | https://www.nist.gov/cyberframework | CSA Cloud Community Profile |
| ISO 27001 | ISO/IEC | https://www.iso.org/standard/27001 | Official CCM mapping |
| ISO 27017/27018 | ISO/IEC | https://www.iso.org/standard/43757.html | Official CCM mapping |
| FedRAMP | GSA | https://www.fedramp.gov/ | Via NIST 800-53 → CCM |
| CIS Controls v8 | CIS | https://www.cisecurity.org/controls | Official CCM mapping |
| CCPA/CPRA | CA AG | https://oag.ca.gov/privacy/ccpa | DSP, GRC, IAM |
| LGPD | Brazil | https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm | DSP, GRC, IAM |
| SOX IT | SEC/PCAOB | https://www.sec.gov/spotlight/sarbanes-oxley.htm | A&A, CCC, GRC, LOG |
| OWASP Top 10 | OWASP | https://owasp.org/Top10/ | AIS, IAM, LOG, TVM |
| CSA CCM v4.1 | CSA | https://cloudsecurityalliance.org/artifacts/cloud-controls-matrix-v4 | Native |

**Unlisted frameworks**: Check https://cloudsecurityalliance.org/research/cloud-controls-matrix/ for existing mappings first. If none, manually map to closest CCM domains and document rationale.

## Cloud Provider Artifacts

| Provider | Source | Note |
|----------|--------|------|
| AWS | AWS Artifact + https://aws.amazon.com/compliance/ | STAR Level 2 |
| Azure | Compliance Manager + https://learn.microsoft.com/en-us/azure/compliance/ | STAR Level 2 |
| GCP | https://cloud.google.com/security/compliance | STAR Level 2 |

---

## Process

### Step 1: Ask Input Mode

```markdown
**How would you like to create your extension?**
- A) Name a standard (HIPAA, PCI-DSS, GDPR, etc.) — I'll resolve via CCM and generate
- B) Point me to folder(s) with existing rule docs — I'll read and structure them
- C) Describe your rules — coding standards, business rules, team processes, or anything else

[Answer]:
```

**Option B details**: User provides folder path(s). AI reads `.md`, `.txt`, `.yaml`, `.json` files (skips binaries, >100KB, hidden files). Cannot read PDFs — ask user to convert. Scan → list files → read → classify rule type → ask clarifying questions for ambiguities → if compliance: map to CCM → proceed to Step 3.

**Option C**: User describes rules → classify rule type → if compliance: map to CCM; if not: proceed directly → Step 3.

**Option A**: Classify as compliance → proceed to Step 2.

### Step 2: CCM-Anchored Resolution (compliance/security rules only — skip for other types)

1. **CCM Domain Lookup** — identify relevant domains from the table above (e.g., HIPAA → primary: DSP, CEK, IAM, LOG, SEF; secondary: AIS, BCR, HRS)
2. **Official Mapping** — use CSA's published CCM ↔ framework mapping if available; otherwise manually map and document rationale
3. **Official Source** — link issuing body URL from the registry into `overview.md`
4. **Provider Artifacts** — if cloud platform known, reference provider's compliance docs
5. **Gap Fill** — only if steps 1-4 leave gaps, search web. Note gap-fill sources separately in `overview.md`

If no tools available: use CCM mappings + training data, cite official URLs, tell user to verify.

### Step 3: Scoping Questions

Universal (all standards):
1. **App type**: Web, API, mobile, data pipeline, infrastructure, full-stack, other
2. **Language(s)**: TypeScript, Python, Java, Go, C#, Rust, multiple, undecided
3. **Cloud platform**: AWS, Azure, GCP, multi-cloud, on-premises, N/A
4. **Strictness**: Maximum (all MUST), Balanced (critical=MUST, others=SHOULD), Advisory (all SHOULD/MAY)
5. **Phases**: All (recommended), or user picks specific phases

Then standard-specific questions informed by CCM domain mapping: data/assets in scope, relevant CCM domains, user's role, compliance tier. Use `[Answer]:` format.

### Step 4: Generate Extension

Output at `aidlc-docs/extensions/[category]-[standard-name]/` (lowercase, hyphens). This is a fixed, IDE-agnostic location — always the same path regardless of which IDE or AI tool is being used.

**If compliance/security**: Every control in every file MUST include dual references: `[Framework Ref] → CCM [Domain]-[ID]`.

**If non-compliance**: No CCM references needed. Use the rule's own categorization (e.g., "STYLE-01: Use 2-space indentation" or "BIZ-RULE-03: Loan amounts require manager approval above $50K").

### Step 5: Review

Show: file list, phases, strictness, CCM domains covered, sources. Offer: approve / change / discard.

### Step 6: Enable

On approval: add to `aidlc-docs/enabled-extensions.md`, log in `audit.md`.


---

## Extension Structure

### Folder Layout

```
aidlc-docs/extensions/[category]-[standard-name]/
├── rule-manifest.yaml    # REQUIRED: metadata, triggers, stage-to-file mapping
├── overview.md           # REQUIRED: scope, sources, rule type
├── ccm-mapping.md        # IF compliance/security — full framework ↔ CCM crosswalk
├── requirements.md       # IF requirements-analysis selected
├── stories.md            # IF user-stories selected
├── design.md             # IF application-design OR functional-design selected
├── nfr-additions.md      # IF nfr-requirements OR nfr-design selected
├── infrastructure.md     # IF infrastructure-design selected
├── code-guidelines.md    # IF code-generation selected
└── testing.md            # IF build-and-test selected
```

Only generate files for selected phases. Manifest MUST only reference files that exist. `ccm-mapping.md` is only generated for compliance/security/privacy extensions.

### `rule-manifest.yaml`

```yaml
name: "[category]-[standard-name]"
version: "1.0.0"
displayName: "[Standard/Ruleset] Guidelines"
description: "[One sentence]"
author: "Generated by extension-generator"
category: "[security|compliance|process|quality|architecture]"
rule_type: "[compliance|coding|business|architecture|process]"
ccm_version: "4.1"                         # ONLY for compliance/security types
ccm_domains: ["DSP", "CEK", "IAM"]         # ONLY for compliance/security types

triggers:
  suggest_when:
    - project_type: ["web", "api", "microservice"]
    - has_requirements_keyword: ["keyword1", "keyword2"]
  always_available: true
  auto_enable: false

applies_to:
  inception:
    - stage: "requirements-analysis"
      file: "requirements.md"
      action: "append"
    - stage: "user-stories"
      file: "stories.md"
      action: "append"
    - stage: "application-design"
      file: "design.md"
      action: "append"
  construction:
    - stage: "functional-design"
      file: "design.md"
      action: "append"
    - stage: "nfr-requirements"
      file: "nfr-additions.md"
      action: "append"
    - stage: "nfr-design"
      file: "nfr-additions.md"
      action: "append"
    - stage: "infrastructure-design"
      file: "infrastructure.md"
      action: "append"
    - stage: "code-generation"
      file: "code-guidelines.md"
      action: "append"
    - stage: "build-and-test"
      file: "testing.md"
      action: "append"

priority: 30
conflicts_with: []
depends_on: []
tags: ["ccm-mapped"]
```

Remove stages/files the user didn't select.


### `overview.md`

Must include: Purpose (2-3 sentences), Rule Type (compliance/coding/business/architecture/process), Scope (standard or ruleset name, app type, languages, cloud, strictness, phases), Phase summary table. **If compliance**: add official source URL, CCM version, CCM Domain Coverage table, Authoritative Sources table, verification warning. **If non-compliance**: add rule source (team wiki, style guide, business requirements doc, etc.) and rule categorization scheme.

### `ccm-mapping.md` (compliance/security only)

Must include: Mapping methodology (source, CCM version, framework version, date), Control Crosswalk table (framework ID → name → CCM domain → CCM IDs → confidence: Official/High/Medium/Low), Unmapped Controls table (framework controls with no CCM equivalent), CCM Controls Without Framework Equivalent table. **Not generated for non-compliance extensions.**

### Stage Content Files — Universal Template

All stage files (`requirements.md`, `stories.md`, `design.md`, `nfr-additions.md`, `infrastructure.md`, `code-guidelines.md`, `testing.md`) follow this single pattern. Substitute `{STAGE}` and `{CONTENT_TYPE}` per the table below.

```markdown
## [Standard/Ruleset] - {STAGE} Guidelines

**Extension**: [name] v1.0.0 | **Applies To**: {STAGE} | **Type**: [compliance|coding|business|architecture|process]

---

### {CONTENT_TYPE}

#### [Control/Rule Area]: [Name]

**Reference**: [Framework Control ID or internal Rule ID, e.g., "HIPAA §164.312(a)(1)" or "STYLE-03"]
**CCM Mapping**: [CCM Domain-ID] ← ONLY for compliance/security types; omit for others

- [MUST/SHOULD/MAY] [Specific actionable item]
- [MUST/SHOULD/MAY] [Specific actionable item]

(Repeat per control area)

### {STAGE}-Specific Additions

(Stage-specific tables/patterns/scenarios — see substitution table below)

### Checklist

- [ ] [Verifiable item (CCM Domain-ID)]
- [ ] [Verifiable item (CCM Domain-ID)]

---

*[name] extension (CCM-anchored)*
```

**Substitution table** — what varies per stage:

| File | {STAGE} | {CONTENT_TYPE} | Stage-Specific Additions |
|------|---------|---------------|--------------------------|
| requirements.md | Requirements Analysis | Compliance Requirements | Additional Questions (`[Answer]:` format) |
| stories.md | User Stories | Compliance Acceptance Criteria | Compliance-specific user stories (`As a [role]...`) |
| design.md | Design | Architectural Constraints | Patterns table (pattern/purpose/CCM/trigger), Anti-patterns table (anti-pattern/risk/CCM violated/alternative) |
| nfr-additions.md | NFR | Compliance NFRs | Add Metric per NFR, Tool/Technology Requirements table |
| infrastructure.md | Infrastructure | Infrastructure Requirements | Cloud Service Requirements table (category/requirement/CCM/services) |
| code-guidelines.md | Code Generation | Mandatory Coding Requirements | Secure Coding Patterns (✅/❌ code examples in user's language, 3-8 lines each) |
| testing.md | Build and Test | Compliance Testing | Scans/Tools table, Test Scenarios, CI/CD Compliance Gates |

---

## Content Quality Rules

1. **Traceable**: Compliance items carry framework ref AND CCM control ID. Non-compliance items carry their own rule ID scheme (e.g., STYLE-01, BIZ-03, ARCH-02)
2. **Specific**: Actionable items only. "Encrypt PII at rest AES-256 (HIPAA §164.312(a)(2)(iv) → CEK-03)" or "STYLE-01: Use 2-space indentation for all TypeScript files" — not "ensure quality"
3. **Real references**: Actual control/rule IDs — no vague pointers
4. **Tailored**: Code examples match user's language, infra matches user's cloud platform
5. **Strictness-aware**: MUST/SHOULD/MAY per scoping answer
6. **Verifiable checklists**: Every `- [ ]` is pass/fail checkable
7. **Official sources only** (compliance): CCM mappings + issuing body text over blogs
8. **Code in code-guidelines.md only**: Other files = guidance, not code
9. **Gaps documented** (compliance): Unmapped controls go in `ccm-mapping.md`, never silently dropped
10. **Baseline dedup**: If overlap with security-baseline (SECURITY-01–15), reference the baseline rule ID instead of duplicating

## Edge Cases

- **No tools**: CCM mappings + training data + official URLs. Tell user to verify.
- **Unknown standard**: Ask for docs or description. Map to CCM where possible.
- **Same-name extension exists**: Offer overwrite, rename, or cancel.
- **Broad standard** (NIST 800-53): Ask which control families matter. Use CCM to suggest focused subset.
- **Multiple standards**: Separate extensions, note shared CCM domains to flag overlap.
- **No CCM mapping exists**: Manual map to closest domains, confidence = Medium/Low, document rationale.
- **Incomplete user docs**: Clarifying questions → map what exists → note gaps.
- **Contradictory user docs**: Flag, ask precedence, document resolution.
- **Non-compliance rules**: No CCM mapping, no official source registry lookup. Generate directly using the user's own rule IDs and categories. Still follow the universal template structure.
- **Mixed input** (compliance + non-compliance): Split into separate extensions by type. Example: user provides HIPAA rules + coding style guide → generate `compliance-hipaa/` (with CCM) + `quality-coding-standards/` (without CCM).

---

*v3.0.0 — CCM-anchored for compliance; direct generation for coding, business, architecture, and process rules.*
