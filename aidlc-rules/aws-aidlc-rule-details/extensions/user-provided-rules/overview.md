# User-Provided Rules Injection

**Extension**: user-provided-rules v1.0.0
**Applies To**: All AI-DLC Phases (Inception + Construction)

---

## Purpose

Users point to folder(s) containing their own rules (compliance, security, internal standards). The AI reads, classifies by phase, and injects at each stage. No pre-built content — users supply their own.

---

## Process

### Step 1: Collect Rule Folder Paths

When enabled, ask the user for folder path(s) containing their rules. Accept single or multiple folders, relative or absolute paths. Supported file types: `.md`, `.yaml`, `.yml`, `.txt`, `.json`.

### Step 2: Scan and Classify

For each folder, recursively read all supported files. Classify each file's content by AI-DLC phase:

| Content Type | Inject At |
|-------------|-----------|
| Requirements, constraints, data policies | Requirements Analysis |
| User-facing compliance criteria | User Stories |
| Architectural/deployment constraints | Application Design, Infrastructure Design |
| Design patterns, data flow rules | Functional Design, NFR Design |
| NFR standards (perf, security, availability) | NFR Requirements |
| Coding standards, secure coding, naming | Code Generation |
| Testing, audit, scan requirements | Build and Test |
| Cross-cutting / general | All applicable phases |

A single file MAY map to multiple phases. When ambiguous, include in MORE phases rather than fewer.

### Step 3: Present Classification and Get Approval

Generate `aidlc-docs/user-provided-rules-summary.md` listing each source file, its classification, and a brief description. Present to user for review. Wait for approval before proceeding.

### Step 4: Inject at Each Phase

At every AI-DLC phase where rules apply:

1. Note which user rules are active for this phase
2. Incorporate rule content into phase decisions and outputs
3. Cite sources inline: *(Source: hipaa-rules/data-protection.md)*
4. Log rule application in `audit.md`

### Precedence

- Core AI-DLC guidance = baseline process
- User-provided rules ADD on top, never remove core steps
- User rules take precedence over generic extension guidance when more specific
- Conflicts between user rules are flagged for user resolution

### Edge Cases

- **Empty folders**: Inform user, continue without
- **50+ files or very large content**: Ask user to prioritize, process priority files first
- **Conflicting rules across folders**: Flag conflict, ask user which takes precedence
- **Content that doesn't map to any phase**: Classify as cross-cutting general context

---

*This content is provided by the user-provided-rules extension.*
