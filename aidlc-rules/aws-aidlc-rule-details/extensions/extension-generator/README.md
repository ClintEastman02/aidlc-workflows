# Extension Generator

**Version**: 1.0.0 | **Status**: Experimental

## Overview

The Extension Generator is an experimental extension that allows users to create custom extension rules for their AI-DLC workflows. It currently focuses on **regulatory and compliance impact** (e.g., HIPAA, PCI-DSS, GDPR, SOC 2, NIST, OWASP), but the underlying pattern is extensible to any internal rules creation based on documentation resources — including coding standards, business rules, architecture patterns, and team processes.

## How It Works

1. **Opt-in**: The generator is gated behind an opt-in prompt during Requirements Analysis. It only activates when explicitly requested.
2. **Classification**: User input is classified by rule type (compliance, coding, business, architecture, or process) to determine the appropriate generation path.
3. **Scoping**: Targeted questions gather context — strictness level, applicable phases, technology stack, and cloud platform.
4. **Generation**: Extension folders are created with structured rule files, manifests, and (for compliance rules) CCM v4.1 normalization mappings.

### Two Entry Paths

- **Recommended Path** — The extension analyzes your project context and recommends applicable extensions based on detected domain keywords.
- **Manual Path** — You specify directly by naming a standard, pointing to existing rule documents, or describing your rules.

## Output

Generated extensions are placed at:

```
aidlc-docs/extensions/[category]/[standard-name]/
```

Each extension includes a `rule-manifest.yaml`, an `overview.md`, and stage-specific guideline files tailored to the phases you select. Compliance extensions additionally include a `ccm-mapping.md` with full framework-to-CCM crosswalk.

## Important Disclaimers

> **This is an experimental feature.** The Extension Generator uses AI to produce rule sets based on known frameworks and user-provided documentation. While it aims for accuracy, the generated output is a **starting point** — not a finished compliance or governance artifact.

> **All generated rules must be reviewed and approved by the appropriate legal, security, and technical resources within your organization before being adopted.** This includes but is not limited to:
>
> - **Legal counsel** for regulatory and privacy frameworks (HIPAA, GDPR, CCPA, etc.)
> - **Security teams** for security controls and vulnerability management rules
> - **Compliance officers** for audit-related standards (SOC 2, PCI-DSS, FedRAMP, etc.)
> - **Technical leads** for coding standards, architecture patterns, and infrastructure rules
>
> The AI-DLC team and contributors are **not responsible** for compliance decisions made based on generated output. Generated rules do not constitute legal advice.

## Limitations

- **No PDF ingestion** — If your source documentation is in PDF format, convert it to Markdown or plain text before providing it to the generator.
- **Files over 100KB are skipped** — Large files and binaries are excluded from automated scanning.
- **CCM mapping confidence varies** — Mappings sourced from official CSA crosswalks are marked as "Official" or "High" confidence. Manual mappings are marked "Medium" or "Low" and should be verified against authoritative sources.
- **One-time context** — The extension loads, generates extension(s), then exits. It does not persist between workflow stages.
- **AI knowledge limits** — For unlisted or niche frameworks, the generator relies on user-provided documentation and may require additional clarification.

## Files in This Directory

| File | Purpose |
|------|---------|
| `generator-extension.md` | Full extension definition — entry points, classification logic, generation templates, and quality rules |
| `rule-manifest.yaml` | Metadata and trigger configuration for the extension generator itself |
| `extension-generator.opt-in.md` | Opt-in prompt shown during Requirements Analysis |
| `ccm-reference.md` | CSA Cloud Controls Matrix v4.1 reference — loaded only for compliance/security rule generation |

## Contributing

If you extend this generator or create new rule types, please follow the existing structure and update the `rule-manifest.yaml` accordingly. When adding compliance framework support, ensure mappings reference official issuing body sources per the registry in `ccm-reference.md`.
