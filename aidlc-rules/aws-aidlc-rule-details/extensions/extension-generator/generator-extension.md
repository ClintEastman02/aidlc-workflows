# Extension Generator Extension

**Extension**: extension-generator v1.0.0 | **One-time per invocation** | **IDE-Agnostic**

## Purpose

Generates AI-DLC extension folders for any ruleset — compliance frameworks, security standards, coding conventions, business rules, team processes, or anything else. For security/compliance/privacy rules, loads `ccm-reference.md` and uses CSA CCM v4.1 as the normalization layer. For non-compliance rules, generates directly without CCM overhead.

**Context behavior**: This extension loads, generates extension(s), then exits context. It does not persist between stages. If another extension is needed later, the extension is re-invoked fresh.

**Activation**: This extension is gated by `extension-generator.opt-in.md`. It only loads into context when the user opts in during Requirements Analysis.

---

## Entry Point

When this extension loads, the user has already opted in via one of two paths (A or B from the opt-in prompt). Present the appropriate flow based on their choice.

### Recommended Path (opt-in answer A)

Used when the user wants the extension to analyze project context and recommend extensions.

1. **Analyze project context** — scan for domain keywords that indicate applicable extensions:
   - Financial/banking/payments → PCI-DSS, SOC 2
   - Healthcare/medical/patient → HIPAA
   - EU users/EU data → GDPR
   - US consumer data → CCPA/CPRA
   - Government/federal → FedRAMP, NIST 800-53
   - General web/API → OWASP Top 10
   - No compliance signals → skip compliance, check for coding/architecture/process needs

2. **Present recommendations** — show the user what was detected and offer:

```markdown
Based on your project context, we recommend the following extensions:

[List recommended extensions with brief rationale]

A) Use these recommended extensions
B) Modify our recommendations — add, remove, or adjust
C) Skip extension generation entirely

[Answer]:
```

3. **If A** → proceed to Rule Type Classification for each recommended extension, generate one at a time.
4. **If B** → user modifies the list, then proceed to generation for the final set.
5. **If C** → record choice in `extension-discovery.md`, exit context.

### Manual Path (opt-in answer B)

Used when the user knows what they need and wants to specify directly.

```markdown
**How would you like to create your extension?**
- A) Name a standard (HIPAA, PCI-DSS, GDPR, etc.)
- B) Point me to folder(s) with existing rule docs — I'll read and structure them
- C) Describe your rules — coding standards, business rules, team processes, or anything else

[Answer]:
```

**Option A**: Classify as compliance → load `ccm-reference.md` → proceed to Scoping Questions.
**Option B**: User provides folder path(s). AI reads `.md`, `.txt`, `.yaml`, `.json` files (skips binaries, >100KB, hidden files). Cannot read PDFs — ask user to convert. Scan → list files → read → classify rule type → ask clarifying questions for ambiguities → if compliance: load `ccm-reference.md` → proceed to Scoping Questions.
**Option C**: User describes rules → classify rule type → if compliance: load `ccm-reference.md` → proceed to Scoping Questions.

---

## Rule Type Classification

Before generating, classify what the user is asking for:

| Rule Type | Category | CCM Applies? | Examples |
|-----------|----------|-------------|----------|
| Security/Compliance/Privacy | `security`, `compliance` | **Yes** — load `ccm-reference.md`, full CCM resolution | HIPAA, PCI-DSS, GDPR, SOC 2, NIST, ISO 27001, OWASP |
| Coding Standards | `quality` | **No** | Style guides, linting rules, naming conventions, language idioms |
| Business Rules | `process` | **No** | Approval workflows, SLA requirements, domain logic constraints |
| Architecture Standards | `architecture` | **No** (unless security-related) | API design patterns, microservice boundaries, data modeling rules |
| Team/Org Process | `process` | **No** | PR review rules, branching strategy, documentation requirements |
| Mixed | Multiple extensions | **Split** — CCM for compliance parts, direct for the rest | "Our HIPAA rules plus our coding standards" |

**Detection heuristic**: If the user's input mentions a known compliance framework, or contains keywords like encrypt, audit, access control, compliance, regulation, privacy, breach, vulnerability — classify as security/compliance and load `ccm-reference.md`. Otherwise, classify as non-compliance.

For **mixed** inputs: split into separate extensions by type. Don't force coding conventions through CCM.

---

## Scoping Questions

Questions adapt based on rule type classification. Only ask what's relevant.

### Universal (all types)

1. **Strictness**: Maximum (all MUST), Balanced (critical=MUST, others=SHOULD), Advisory (all SHOULD/MAY)
2. **Phases**: All (recommended), or user picks specific phases

### Compliance/Security

3. **App type**: Web, API, mobile, data pipeline, infrastructure, full-stack, other
4. **Language(s)**: TypeScript, Python, Java, Go, C#, Rust, multiple, undecided
5. **Cloud platform**: AWS, Azure, GCP, multi-cloud, on-premises, N/A
6. Standard-specific questions informed by CCM domain mapping: data/assets in scope, relevant CCM domains, user's role, compliance tier

### Coding Standards

3. **Language(s)**: TypeScript, Python, Java, Go, C#, Rust, multiple, undecided
4. **App type**: Web, API, mobile, data pipeline, infrastructure, full-stack, other

### Architecture Standards

3. **App type**: Web, API, mobile, data pipeline, infrastructure, full-stack, other
4. **Language(s)**: TypeScript, Python, Java, Go, C#, Rust, multiple, undecided
5. **Cloud platform** (if relevant): AWS, Azure, GCP, multi-cloud, on-premises, N/A

### Business Rules / Team Process

3. No additional technical questions — proceed with what the user described.

Use `[Answer]:` format for all questions.

---

## Generation

### Output Path

Use the workspace path detected by the core workflow. If running via Manual Path and no workspace context exists, default to `aidlc-docs/extensions/`. Create the directory if it doesn't exist.

Output at `aidlc-docs/extensions/[category]/[standard-name]/` (lowercase, hyphens).

**If compliance/security**: Load `ccm-reference.md` and follow the CCM Resolution Steps. Every control in every file MUST include dual references: `[Framework Ref] → CCM [Domain]-[ID]`.

**If non-compliance**: Do NOT load `ccm-reference.md`. Use the rule's own categorization (e.g., "STYLE-01: Use 2-space indentation" or "BIZ-RULE-03: Loan amounts require manager approval above $50K").

### Extension Discovery File

After generation, create or update `aidlc-docs/extensions/extension-discovery.md` with:

```markdown
# Extension Discovery

## Project Context
- **App type**: [from workflow or scoping]
- **Domain**: [detected domain keywords]
- **Generated on**: [date]

## Recommendations
[What the extension recommended and why]

## User Choice
[A/B/C and any modifications]

## Generated Extensions
| Extension | Category | Rule Type | Status |
|-----------|----------|-----------|--------|
| [name] | [category] | [type] | Generated |

## Manual Additions
[Extensions added via Manual Path after initial generation]
```

Update this file each time the extension runs — append to Generated Extensions or Manual Additions as appropriate.

### Review

Show: file list, phases covered, strictness level, sources used. If compliance: CCM domains covered.
Offer: **approve** / **change** / **discard**.

On approval, generation is complete. Extension exits context.

---

## Extension Structure

### Folder Layout

```
aidlc-docs/extensions/[category]/[standard-name]/
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
name: "[standard-name]"
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

Remove stages/files the user didn't select. Remove `ccm_version`, `ccm_domains`, and `tags: ["ccm-mapped"]` for non-compliance extensions.

### `overview.md`

Must include: Purpose (2-3 sentences), Rule Type (compliance/coding/business/architecture/process), Scope (standard or ruleset name, app type, languages, cloud, strictness, phases), Phase summary table. **If compliance**: add official source URL, CCM version, CCM Domain Coverage table, Authoritative Sources table, verification warning. **If non-compliance**: add rule source (team wiki, style guide, business requirements doc, etc.) and rule categorization scheme.

### `ccm-mapping.md` (compliance/security only)

Must include: Mapping methodology (source, CCM version, framework version, date), Control Crosswalk table (framework ID → name → CCM domain → CCM IDs → confidence: Official/High/Medium/Low), Unmapped Controls table (framework controls with no CCM equivalent), CCM Controls Without Framework Equivalent table. **Not generated for non-compliance extensions.**

### Stage Content Files — Universal Template

All stage files follow this single pattern. Substitute `{STAGE}` and `{CONTENT_TYPE}` per the substitution table.

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

- [ ] [Verifiable item]
- [ ] [Verifiable item]
```

**Substitution table** — what varies per stage:

| File | {STAGE} | {CONTENT_TYPE} | Stage-Specific Additions |
|------|---------|---------------|--------------------------|
| requirements.md | Requirements Analysis | Requirements | Additional Questions (`[Answer]:` format) |
| stories.md | User Stories | Acceptance Criteria | User stories (`As a [role]...`) |
| design.md | Design | Architectural Constraints | Patterns table (pattern/purpose/trigger), Anti-patterns table (anti-pattern/risk/alternative) |
| nfr-additions.md | NFR | NFR Additions | Metric per NFR, Tool/Technology Requirements table |
| infrastructure.md | Infrastructure | Infrastructure Requirements | Cloud Service Requirements table (category/requirement/services) |
| code-guidelines.md | Code Generation | Mandatory Coding Requirements | Coding Patterns (code examples in user's language, 3-8 lines each) |
| testing.md | Build and Test | Testing Requirements | Scans/Tools table, Test Scenarios, CI/CD Gates |

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

---

## Edge Cases

- **No tools**: CCM mappings + training data + official URLs. Tell user to verify.
- **Unknown standard**: Ask for docs or description. Map to CCM where possible.
- **Same-name extension exists**: Check `extension-discovery.md`, offer overwrite, rename, or cancel.
- **Broad standard** (NIST 800-53): Ask which control families matter. Use CCM to suggest focused subset.
- **Multiple standards**: Generate separate extensions one at a time, note shared CCM domains to flag overlap.
- **No CCM mapping exists**: Manual map to closest domains, confidence = Medium/Low, document rationale.
- **Incomplete user docs**: Clarifying questions → map what exists → note gaps.
- **Contradictory user docs**: Flag, ask precedence, document resolution.
- **Non-compliance rules**: No CCM mapping, no `ccm-reference.md` load. Generate directly using the user's own rule IDs and categories. Still follow the universal template structure.
- **Mixed input** (compliance + non-compliance): Split into separate extensions by type. Example: user provides HIPAA rules + coding style guide → generate `compliance/hipaa/` (with CCM) + `quality/coding-standards/` (without CCM).
- **Re-invocation**: Read `extension-discovery.md` to see what already exists before recommending or generating.

---

*v1.0.0 — CCM-anchored for compliance; direct generation for coding, business, architecture, and process rules.*
