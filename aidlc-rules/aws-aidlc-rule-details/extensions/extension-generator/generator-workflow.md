# Extension Generator Skill

**Skill**: extension-generator v2.0.0
**Applies To**: Workflow Planning Stage (one-time generation)
**IDE-Agnostic**: Works with any AI coding tool (Q Developer, Kiro, Cursor, Cline, Claude Code, Copilot)

---

## Purpose

Generates a complete AI-DLC extension folder for any compliance framework, security standard, or custom ruleset. Uses available tools (web search, fetch, MCP servers) to research the latest requirements before generating.

---

## MANDATORY: Research Before Generating

**Do NOT rely solely on training data for compliance content.** Standards change. Before generating any extension, the AI model MUST attempt to research current requirements using whatever tools are available in the current environment.

### Tool Discovery

Check what tools are available. Different IDEs provide different capabilities:

| Capability | How to Check |
|-----------|-------------|
| Web search | Try using any available web search tool (web_search, remote_web_search, etc.) |
| URL fetch | Try using any available fetch tool (mcp_fetch_fetch, web_fetch, etc.) |
| MCP servers | Try using any available MCP tools (Context7, AWS docs, etc.) |
| File read | Always available — read user-provided docs from local paths |

**The AI model should attempt to use whatever tools exist.** If a tool isn't available, skip it and move on. If NO research tools are available, fall back to training data knowledge and inform the user.

### Research Strategy

For the standard the user names, attempt these in order:

1. **Search for the official standard documentation** — look for the latest version, recent updates, current control lists
2. **Search for implementation guides** — practical guidance for applying the standard to software
3. **Search for recent changes or updates** — any new requirements added in the last 1-2 years
4. **Fetch specific pages** if search returns relevant URLs — extract control lists, requirement details, implementation guidance

**What to research per standard type:**

| Standard Type | Research Focus |
|--------------|---------------|
| Compliance (HIPAA, PCI-DSS, SOX) | Current control requirements, recent rule changes, enforcement guidance |
| Privacy (GDPR, CCPA, LGPD) | Data subject rights, processing requirements, recent enforcement trends |
| Government (FedRAMP, NIST, FISMA) | Current control baselines, impact levels, recent revisions |
| Security (OWASP, CIS, ISO 27001) | Current top risks/controls, latest version changes, implementation benchmarks |
| Industry-specific | Current regulatory requirements, recent updates, sector-specific controls |

**Log research results** in the generated extension's `overview.md` so users know what sources informed the content.

---

## Process

### Step 1: Ask What Standard the User Needs

Present categories (Healthcare, Financial, Government, Privacy, Security, Industry, Internal/Custom) and ask the user to name their standard. Accept any standard. Multiple standards = generate separate extensions for each.

Also offer the option to provide existing documentation:

```markdown
**How would you like to create your extension?**

- A) Name a standard (HIPAA, PCI-DSS, GDPR, SOC 2, etc.) — I'll research it and generate rules
- B) Point me to folder(s) with your existing rule docs — I'll read them and generate a structured extension
- C) Describe your custom rules — I'll generate an extension from your description

[Answer]:
```

**If user chooses B (existing docs):**

#### Where to put the files

Users place their rule documents in a folder anywhere in their workspace. There is no required location, but we recommend:

```
<my-project>/
├── .aidlc-rule-details/         # AI-DLC rules (already exists)
├── aidlc-docs/                  # AI-DLC outputs (already exists)
├── custom-rules/                # RECOMMENDED: put your rule docs here
│   ├── hipaa/
│   │   ├── data-protection.md
│   │   └── access-control.md
│   └── internal-standards/
│       └── coding-guidelines.md
└── [project code]
```

Any folder path works. The user provides the path(s) and the AI reads from there.

#### What the AI reads

Supported file types:
- `.md` (Markdown) — preferred, best for structured rules
- `.txt` (Plain text) — simple rule lists, policies
- `.yaml` / `.yml` (YAML) — structured control definitions
- `.json` (JSON) — structured rule data

Skipped automatically:
- Binary files (images, PDFs, compiled files)
- Files larger than 100KB (flag to user, ask if they want to include)
- Hidden files/folders (`.git`, `.DS_Store`, etc.)

**Note on PDFs**: The AI cannot read PDF files directly. If the user's rules are in PDF format, ask them to convert to markdown first or paste the key content into `.md` files.

#### Process for option B

1. Ask for folder path(s):
```markdown
Where are your rule documents located? Provide one or more folder paths relative to your project root.

Example: `custom-rules/hipaa/`, `custom-rules/internal-standards/`

[Answer]:
```

2. Scan the folder(s) and list what was found:
```markdown
I found the following files:

| # | File | Size | Type |
|---|------|------|------|
| 1 | custom-rules/hipaa/data-protection.md | 4KB | Markdown |
| 2 | custom-rules/hipaa/access-control.md | 2KB | Markdown |
| 3 | custom-rules/internal-standards/coding-guidelines.md | 6KB | Markdown |

I'll now read these and analyze the content.
```

3. Read each file and build an understanding of: what standard/framework it covers, key control areas, scope, and which AI-DLC phases each rule maps to.

4. **Ask clarifying questions** for anything ambiguous. Common situations that require clarification:

```markdown
I have a few questions about your rule documents:

**Question DOC-1**: In `data-protection.md`, you mention "sensitive data must be encrypted." Which encryption standard should be used?
- A) AES-256
- B) AES-128
- C) Whatever the cloud provider default is
- D) It's specified elsewhere (please point me to it)

[Answer]:

**Question DOC-2**: Your `access-control.md` references "role-based access" but doesn't list the roles. Should I:
- A) Define standard roles based on the framework (e.g., Admin, Clinician, Patient for HIPAA)
- B) Leave role definitions for you to fill in later
- C) The roles are defined in another document (please provide path)

[Answer]:

**Question DOC-3**: I found rules about testing in `coding-guidelines.md` and also in `access-control.md`. Should I:
- A) Combine them into one testing section
- B) Keep them separate (testing from each source applied independently)

[Answer]:
```

Ask clarifying questions when:
- A rule is vague or could be interpreted multiple ways
- A rule references something not defined in the provided docs
- Rules from different files appear to conflict
- A rule's scope is unclear (does it apply to all code or just specific areas?)
- Technical specifics are missing (which encryption algorithm, which auth protocol, etc.)

5. Skip Step 2 (research) — the user's docs ARE the source
6. Proceed to Step 3 (scoping questions) with the doc content as context, then Step 4 (generate)
7. The generated extension cites the user's files as sources in `overview.md`

**If user chooses C (describe rules):**
1. Ask the user to describe their rules, standards, or guidelines
2. Skip Step 2 (research)
3. Proceed to Step 3 (scoping questions) with the description as context, then Step 4 (generate)

**If user chooses A (name a standard):**
Proceed to Step 2 (research) as normal.

### Step 2: Research the Standard

Before asking scoping questions, research the standard using available tools:

1. **Search** for "[standard name] latest requirements [current year]"
2. **Search** for "[standard name] software development compliance checklist"
3. **Fetch** any official documentation URLs found
4. **Note** the current version, last update date, and key control areas found

If research tools are unavailable, state: "I don't have web access in this environment, so I'll use my built-in knowledge of [standard]. Please verify the generated content against the latest official documentation."

### Step 3: Ask Scoping Questions

Universal questions for ALL standards:

1. **Application type**: Web, API, mobile, data pipeline, infrastructure, full-stack, other
2. **Programming language(s)**: TypeScript, Python, Java, Go, C#, Rust, multiple, undecided
3. **Cloud platform**: AWS, Azure, GCP, multi-cloud, on-premises, N/A
4. **Strictness level**: Maximum (all MUST), Balanced (critical=MUST, others=SHOULD), Advisory (all SHOULD/MAY), User reviews after
5. **Phases to cover**: All phases (recommended), design+code only, requirements+design only, code+testing only, user picks specific phases

Then ask standard-specific questions informed by the research results. Focus on:
- What specific data/assets are in scope
- Which areas/controls of the standard are most relevant (list actual control areas found in research)
- The user's role or context within the standard
- Compliance level or tier if applicable

For custom/internal standards: ask the user to describe their rules or provide file paths.

Use the standard `[Answer]:` tag format for all questions.

### Step 4: Generate the Extension Folder

Generate at `.aidlc-rule-details/extensions/[category]-[standard-name]/`.

**Naming**: lowercase with hyphens. Examples: `compliance-hipaa`, `security-fedramp`, `compliance-pci-dss`

**The generated extension MUST follow the exact structure defined in the Reference Extension Structure section below.**

### Step 5: Present for Review

Show the user: file list, phases covered, strictness level, sources used. Offer: approve, request changes, or discard.

### Step 6: Enable

On approval: add to `aidlc-docs/enabled-extensions.md`, log in `audit.md`. The extension is immediately active for remaining phases.

---

## Reference Extension Structure

**Every generated extension MUST conform to this structure.** This is the canonical reference for what a well-formed AI-DLC extension looks like.

### Required Folder Layout

```
.aidlc-rule-details/extensions/[category]-[standard-name]/
├── rule-manifest.yaml          # REQUIRED: metadata, triggers, stage-to-file mapping
├── overview.md                 # REQUIRED: description, scope, sources
├── requirements.md             # IF requirements-analysis phase selected
├── stories.md                  # IF user-stories phase selected
├── design.md                   # IF application-design OR functional-design phase selected
├── nfr-additions.md            # IF nfr-requirements OR nfr-design phase selected
├── infrastructure.md           # IF infrastructure-design phase selected
├── code-guidelines.md          # IF code-generation phase selected
└── testing.md                  # IF build-and-test phase selected
```

Only generate files for phases the user selected in scoping question 5. The `rule-manifest.yaml` MUST only reference files that actually exist.

---

### Complete `rule-manifest.yaml` Reference

This is the full manifest with ALL possible stages. Remove stages/files the user didn't select.

```yaml
name: "[category]-[standard-name]"       # lowercase, hyphens, no spaces
version: "1.0.0"
displayName: "[Standard Full Name] Guidelines"
description: "[One sentence describing what this extension adds]"
author: "Generated by extension-generator"
category: "[security|compliance|process|quality|architecture]"

triggers:
  suggest_when:
    # Match project types relevant to this standard
    - project_type: ["web", "api", "microservice", "library", "cli", "backend"]
    # Match keywords that indicate this standard is relevant
    - has_requirements_keyword: ["keyword1", "keyword2", "keyword3"]
    # Match languages if standard is language-specific
    - programming_language: ["python", "typescript", "java"]
  always_available: true
  auto_enable: false

# CRITICAL: This maps stages to files. Every file listed here MUST exist.
# Every stage listed here will have the file's content appended/prepended at that stage.
applies_to:
  inception:
    - stage: "requirements-analysis"      # Adds compliance requirements
      file: "requirements.md"
      action: "append"
    - stage: "user-stories"               # Adds compliance acceptance criteria
      file: "stories.md"
      action: "append"
    - stage: "application-design"         # Adds architectural constraints
      file: "design.md"
      action: "append"
  construction:
    - stage: "functional-design"          # Adds design patterns/constraints
      file: "design.md"
      action: "append"
    - stage: "nfr-requirements"           # Adds NFR standards
      file: "nfr-additions.md"
      action: "append"
    - stage: "nfr-design"                 # Adds NFR design patterns
      file: "nfr-additions.md"
      action: "append"
    - stage: "infrastructure-design"      # Adds infrastructure constraints
      file: "infrastructure.md"
      action: "append"
    - stage: "code-generation"            # Adds coding rules
      file: "code-guidelines.md"
      action: "append"
    - stage: "build-and-test"             # Adds testing/audit requirements
      file: "testing.md"
      action: "append"

priority: 30          # 1-100. Lower = applied earlier. Compliance: 10-30, Security: 30-50, Quality: 50-100
conflicts_with: []    # Names of extensions that can't coexist
depends_on: []        # Names of extensions that must be enabled first
tags: ["tag1", "tag2"]
```

**How `applies_to` works at runtime:**
- When the AI-DLC workflow reaches a stage (e.g., `code-generation`), it checks all enabled extensions
- If an extension has an `applies_to` entry for that stage, the AI reads the referenced file
- The file content is applied using the `action` (append = add after core content, prepend = add before)
- Multiple extensions can apply to the same stage — they're applied in `priority` order

**Note:** A single file (like `design.md`) CAN be referenced by multiple stages (like `application-design` AND `functional-design`). The same content is injected at both stages.

---

### Complete `overview.md` Reference

```markdown
# [Standard Full Name] Extension

## Purpose

[2-3 sentences: what this extension adds and why, based on scoping answers]

## Scope

- **Standard**: [name and version]
- **Application Type**: [from scoping question 1]
- **Languages**: [from scoping question 2]
- **Cloud Platform**: [from scoping question 3]
- **Strictness**: [from scoping question 4]
- **Phases Covered**: [from scoping question 5]

## What This Extension Covers

| Phase | What's Added |
|-------|-------------|
| Requirements Analysis | [1-line summary of what requirements.md adds] |
| User Stories | [1-line summary of what stories.md adds] |
| Design | [1-line summary of what design.md adds] |
| NFR | [1-line summary of what nfr-additions.md adds] |
| Infrastructure | [1-line summary of what infrastructure.md adds] |
| Code Generation | [1-line summary of what code-guidelines.md adds] |
| Build and Test | [1-line summary of what testing.md adds] |

(Only include rows for phases that were selected)

## Key [Standard] Areas Covered

- [Area/Control Family 1]: [brief description]
- [Area/Control Family 2]: [brief description]
- [Area/Control Family 3]: [brief description]

## Sources

This extension was generated using:
- [Source 1: URL or "AI model training data"]
- [Source 2: URL or description]
- **Research date**: [date]
- **Standard version**: [version found, or "latest available as of [date]"]

⚠️ Compliance standards change. Verify this content against official documentation periodically.

---

*Extension: [name] v1.0.0 — Generated by extension-generator*
```

---

### Stage Content File Templates

Each stage file follows the same structure but with content appropriate to what that stage does in the AI-DLC workflow.

#### `requirements.md` — Requirements Analysis Stage

**Purpose at this stage**: Add compliance-driven requirements that must be captured before design begins.

```markdown
## [Standard] - Requirements Analysis Guidelines

**Extension**: [name] v1.0.0
**Applies To**: Requirements Analysis

---

### Compliance Requirements

#### [Control Area 1]: [Name] (e.g., "Data Protection", "Access Control")

**Objective**: [What this control area requires]
**Reference**: [Control ID, e.g., HIPAA §164.312(a)(1), PCI-DSS Req 3.4]

- [MUST/SHOULD/MAY] [Specific requirement to capture]
- [MUST/SHOULD/MAY] [Specific requirement to capture]

#### [Control Area 2]: [Name]

**Objective**: [What this control area requires]
**Reference**: [Control ID]

- [MUST/SHOULD/MAY] [Specific requirement to capture]
- [MUST/SHOULD/MAY] [Specific requirement to capture]

### Requirements Checklist

- [ ] [Requirement captured: e.g., "Data classification defined for all data types"]
- [ ] [Requirement captured: e.g., "Access control model specified"]
- [ ] [Requirement captured: e.g., "Audit logging requirements documented"]
- [ ] [Requirement captured: e.g., "Encryption requirements specified"]

### Additional Questions

**Question [PREFIX]-R1**: [Compliance-relevant question for requirements phase]
- A) [Option]
- B) [Option]
- C) [Option]
- D) Other (please describe)

[Answer]:

---

*This content is provided by the [name] extension.*
```

#### `stories.md` — User Stories Stage

**Purpose at this stage**: Add compliance-related acceptance criteria to user stories.

```markdown
## [Standard] - User Stories Guidelines

**Extension**: [name] v1.0.0
**Applies To**: User Stories

---

### Compliance Acceptance Criteria

When creating user stories, add these acceptance criteria where applicable:

#### [Control Area]: [Name]

- **AC**: [Specific acceptance criterion, e.g., "Given a user accesses PHI, when the access is logged, then the audit trail includes user ID, timestamp, and data accessed"]
- **AC**: [Specific acceptance criterion]

#### [Control Area]: [Name]

- **AC**: [Specific acceptance criterion]
- **AC**: [Specific acceptance criterion]

### Compliance-Specific User Stories

Consider adding these stories if not already covered:

- As a [role], I need [compliance capability] so that [compliance outcome]
- As a [role], I need [compliance capability] so that [compliance outcome]

### Checklist

- [ ] All stories handling [regulated data type] include compliance acceptance criteria
- [ ] Audit/logging stories exist for [regulated activities]
- [ ] Access control stories cover [required access patterns]

---

*This content is provided by the [name] extension.*
```

#### `design.md` — Application Design + Functional Design Stages

**Purpose at this stage**: Add compliance-driven architectural constraints and design patterns.

```markdown
## [Standard] - Design Guidelines

**Extension**: [name] v1.0.0
**Applies To**: Application Design, Functional Design

---

### Architectural Constraints

#### [Control Area]: [Name]

**Constraint**: [What the architecture must enforce]
**Reference**: [Control ID]

- [MUST/SHOULD/MAY] [Specific design constraint]
- [MUST/SHOULD/MAY] [Specific design constraint]

### Design Patterns to Follow

| Pattern | Purpose | When to Apply |
|---------|---------|--------------|
| [Pattern name] | [What it achieves for compliance] | [Trigger condition] |
| [Pattern name] | [What it achieves for compliance] | [Trigger condition] |

### Anti-Patterns to Avoid

| Anti-Pattern | Risk | Alternative |
|-------------|------|------------|
| [Anti-pattern] | [Compliance risk] | [What to do instead] |
| [Anti-pattern] | [Compliance risk] | [What to do instead] |

### Design Checklist

- [ ] [Design constraint verified: e.g., "Data encryption at rest designed into storage layer"]
- [ ] [Design constraint verified: e.g., "Role-based access control modeled"]
- [ ] [Design constraint verified: e.g., "Audit logging architecture defined"]

---

*This content is provided by the [name] extension.*
```

#### `nfr-additions.md` — NFR Requirements + NFR Design Stages

**Purpose at this stage**: Add compliance-driven non-functional requirements and their design patterns.

```markdown
## [Standard] - NFR Guidelines

**Extension**: [name] v1.0.0
**Applies To**: NFR Requirements, NFR Design

---

### Compliance NFRs

#### [NFR Category]: [Name] (e.g., "Security", "Auditability", "Data Retention")

**Requirement**: [Specific NFR statement]
**Reference**: [Control ID]
**Metric**: [How to measure compliance]

- [MUST/SHOULD/MAY] [Specific NFR item]
- [MUST/SHOULD/MAY] [Specific NFR item]

#### [NFR Category]: [Name]

**Requirement**: [Specific NFR statement]
**Reference**: [Control ID]
**Metric**: [How to measure compliance]

- [MUST/SHOULD/MAY] [Specific NFR item]

### Tool/Technology Requirements

| Requirement | Recommended Tools | Purpose |
|------------|-------------------|---------|
| [Requirement] | [Tool options] | [What it achieves for compliance] |
| [Requirement] | [Tool options] | [What it achieves for compliance] |

### NFR Checklist

- [ ] [NFR defined: e.g., "Encryption standard specified (AES-256)"]
- [ ] [NFR defined: e.g., "Audit log retention period set (minimum X years)"]
- [ ] [NFR defined: e.g., "Access review frequency defined"]

---

*This content is provided by the [name] extension.*
```

#### `infrastructure.md` — Infrastructure Design Stage

**Purpose at this stage**: Add compliance-driven infrastructure constraints and service requirements.

```markdown
## [Standard] - Infrastructure Guidelines

**Extension**: [name] v1.0.0
**Applies To**: Infrastructure Design

---

### Infrastructure Requirements

#### [Control Area]: [Name]

**Reference**: [Control ID]

- [MUST/SHOULD/MAY] [Specific infrastructure requirement]
- [MUST/SHOULD/MAY] [Specific infrastructure requirement]

### Cloud Service Requirements (if applicable)

| Service Category | Compliance Requirement | [Cloud] Service Options |
|-----------------|----------------------|------------------------|
| [Category, e.g., "Encryption"] | [What's required] | [Specific services] |
| [Category, e.g., "Logging"] | [What's required] | [Specific services] |
| [Category, e.g., "Network"] | [What's required] | [Specific services] |

### Infrastructure Checklist

- [ ] [Infra requirement: e.g., "Encryption at rest enabled on all data stores"]
- [ ] [Infra requirement: e.g., "Network segmentation for regulated data"]
- [ ] [Infra requirement: e.g., "Centralized audit logging configured"]
- [ ] [Infra requirement: e.g., "Backup and disaster recovery plan defined"]

---

*This content is provided by the [name] extension.*
```

#### `code-guidelines.md` — Code Generation Stage

**Purpose at this stage**: Add compliance-driven coding standards, patterns, and anti-patterns.

```markdown
## [Standard] - Code Generation Guidelines

**Extension**: [name] v1.0.0
**Applies To**: Code Generation

---

### Mandatory Coding Requirements

#### [Control Area]: [Name]

**Reference**: [Control ID]

- [MUST/SHOULD/MAY] [Specific coding requirement]
- [MUST/SHOULD/MAY] [Specific coding requirement]

### Secure Coding Patterns

```[language]
# ✅ CORRECT: [Description of compliant pattern]
[short code example — 3-8 lines max]

# ❌ WRONG: [Description of non-compliant pattern]
[short code example — 3-8 lines max]
```

(Include 2-4 pattern examples tailored to the user's language from scoping question 2)

### Code Generation Checklist

- [ ] [Code requirement: e.g., "All [regulated data] inputs validated with schema"]
- [ ] [Code requirement: e.g., "Parameterized queries for all database operations"]
- [ ] [Code requirement: e.g., "No [regulated data] in log output"]
- [ ] [Code requirement: e.g., "Error messages do not expose [regulated data]"]
- [ ] [Code requirement: e.g., "[Regulated data] encrypted before storage"]

---

*This content is provided by the [name] extension.*
```

#### `testing.md` — Build and Test Stage

**Purpose at this stage**: Add compliance-driven testing, scanning, and audit requirements.

```markdown
## [Standard] - Testing Guidelines

**Extension**: [name] v1.0.0
**Applies To**: Build and Test

---

### Compliance Testing Requirements

#### [Test Category]: [Name] (e.g., "Security Scanning", "Audit Verification")

**Reference**: [Control ID]

- [MUST/SHOULD/MAY] [Specific test requirement]
- [MUST/SHOULD/MAY] [Specific test requirement]

### Required Scans and Tools

| Scan Type | Tool Options | Frequency | Failure Threshold |
|-----------|-------------|-----------|-------------------|
| [Scan type] | [Tools] | [When to run] | [What blocks deployment] |
| [Scan type] | [Tools] | [When to run] | [What blocks deployment] |

### Compliance Test Scenarios

- [ ] Test: [Specific test scenario, e.g., "Verify audit log captures all PHI access events"]
- [ ] Test: [Specific test scenario, e.g., "Verify encryption at rest for all data stores"]
- [ ] Test: [Specific test scenario, e.g., "Verify access denied for unauthorized roles"]
- [ ] Test: [Specific test scenario, e.g., "Verify [regulated data] not present in error responses"]

### CI/CD Compliance Gates

- [ ] [Gate: e.g., "SAST scan passes with zero critical findings"]
- [ ] [Gate: e.g., "Dependency scan passes with zero critical vulnerabilities"]
- [ ] [Gate: e.g., "Compliance-specific test suite passes"]
- [ ] [Gate: e.g., "Audit log integrity verified"]

---

*This content is provided by the [name] extension.*
```

---

## Content Quality Rules

When populating the templates above with actual standard content:

1. **Specific, not vague**: Every item must be actionable. "Ensure security" is useless. "Encrypt all PII at rest using AES-256 (HIPAA §164.312(a)(2)(iv))" is actionable.
2. **Real control references**: Include actual section/control IDs from the standard where they exist.
3. **Tailored to scoping answers**: If user said Python on AWS, code examples should be Python, infrastructure should reference AWS services.
4. **Respect strictness level**: Use MUST/SHOULD/MAY per the user's scoping question 4 answer.
5. **Verifiable checklists**: Every `- [ ]` item must be something you can check as done or not done.
6. **Research-informed**: Prefer content from research results over training data when available.
7. **Code examples in code-guidelines.md only**: Other files provide guidance, not code. Exception: short inline examples in design.md anti-patterns table.

---

## Edge Cases

- **No research tools available**: Fall back to training data, inform user to verify against official docs.
- **Unknown standard**: Ask user to provide documentation or describe rules. Research what's available.
- **Existing extension with same name**: Offer to overwrite, rename, or cancel.
- **Very broad standard** (e.g., NIST 800-53): Ask which control families are most relevant.
- **Multiple standards**: Generate separate extensions for each, check for conflicts.
- **Outdated research results**: Always include the research date and recommend periodic review.
- **User docs are incomplete**: Ask clarifying questions for gaps. If critical information is missing, generate what's possible and note gaps in `overview.md` for the user to fill in.
- **User docs contain contradictions**: Flag the contradiction, ask the user which rule takes precedence, document the resolution.
- **User docs are in a language the AI can read but not a supported file type**: Ask the user to convert to a supported format or paste the content into a `.md` file.

---

*This skill is part of the AI-DLC extension system and works across all supported platforms.*
