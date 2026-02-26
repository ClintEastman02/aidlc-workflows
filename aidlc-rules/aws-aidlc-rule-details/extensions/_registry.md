# Available Extensions

Extensions add domain-specific rules to AI-DLC phases. They are discovered automatically during Workflow Planning.

## Installed Extensions

### extension-generator (v3.0.0)
IDE-agnostic skill that generates AI-DLC extensions for any ruleset. For compliance/security/privacy rules, maps through CSA CCM v4.1 as the normalization layer with full control traceability. For coding standards, business rules, architecture patterns, and team processes, generates directly without CCM overhead. Three input modes: name a standard (CCM resolution), point to existing docs (auto-classifies rule type), or describe your rules.
- **Category**: process | **Priority**: 5
- **Stages**: Workflow Planning (classifies rule type + generates extensions covering all stages)
- **Auto-suggested**: When compliance, standard, style guide, business rule, or convention keywords detected
- **Rule Types**: compliance (CCM-anchored), coding, business, architecture, process

## How Extensions Work

1. During Workflow Planning, AI scans two locations for extensions:
   - **Built-in**: `{rule-details-dir}/extensions/` (ships with AI-DLC)
   - **Generated**: `aidlc-docs/extensions/` (created by extension-generator, IDE-agnostic fixed path)
2. Extensions are suggested based on project characteristics
3. User enables/disables as needed
4. Enabled extensions inject content at their configured stages (lazy-loaded per stage, not all at once)
5. Extensions ADD to core guidance (never replace)
6. Priority determines application order (lower = earlier)
7. All compliance extensions include CCM traceability (`ccm-mapping.md`) for cross-framework auditability; non-compliance extensions use their own rule ID schemes

## Creating Extensions

Use the `extension-generator` skill to create extensions automatically (compliance, coding, business, architecture, or process rules), or create manually with: `rule-manifest.yaml` (required) + `ccm-mapping.md` (if compliance) + content `.md` files per stage.
