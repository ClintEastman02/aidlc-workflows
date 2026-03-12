# CCM v4.1 Reference

**Load this file ONLY when rule type classification = compliance/security/privacy. Do NOT load for coding standards, business rules, architecture, or process extensions.**

---

## CCM-Anchored Resolution

For rules classified as security/compliance/privacy, follow this deterministic order instead of random web searches:

```
1. CCM Domain Lookup        → relevant CCM v4.1 domain(s) + control IDs
2. Official Framework Mapping → CCM ↔ framework crosswalk (ISO, NIST, PCI, etc.)
3. Official Source of Truth   → issuing body URL for legal/regulatory text
4. Cloud Provider Artifacts   → provider's own CCM/STAR alignment docs
5. Gap Fill (last resort)     → web search only for what CCM doesn't cover
```

**Why**: CCM v4.1 has 207 controls across 17 domains, harmonizes 40+ global standards, and every major cloud provider publishes CCM/STAR alignment. Extensions become cross-framework comparable.

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

---

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

---

## Cloud Provider Artifacts

| Provider | Source | Note |
|----------|--------|------|
| AWS | AWS Artifact + https://aws.amazon.com/compliance/ | STAR Level 2 |
| Azure | Compliance Manager + https://learn.microsoft.com/en-us/azure/compliance/ | STAR Level 2 |
| GCP | https://cloud.google.com/security/compliance | STAR Level 2 |

---

## CCM Resolution Steps (for use during generation)

1. **CCM Domain Lookup** — identify relevant domains from the table above (e.g., HIPAA → primary: DSP, CEK, IAM, LOG, SEF; secondary: AIS, BCR, HRS)
2. **Official Mapping** — use CSA's published CCM ↔ framework mapping if available; otherwise manually map and document rationale
3. **Official Source** — link issuing body URL from the registry into `overview.md`
4. **Provider Artifacts** — if cloud platform known, reference provider's compliance docs
5. **Gap Fill** — only if steps 1-4 leave gaps, search web. Note gap-fill sources separately in `overview.md`

If no tools available: use CCM mappings + training data, cite official URLs, tell user to verify.
