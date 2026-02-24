# Available Extensions

Extensions add domain-specific rules to AI-DLC phases. They are discovered automatically during Workflow Planning.

## Installed Extensions

### extension-generator (v2.0.0)
IDE-agnostic skill that generates structured AI-DLC extensions from any compliance standard, security framework, or custom ruleset. Three input modes: name a standard (AI researches and generates), point to existing rule docs (AI reads and structures), or describe your rules (AI generates from description).
- **Category**: process | **Priority**: 5
- **Stages**: Workflow Planning (researches + generates extensions covering all stages)
- **Auto-suggested**: When compliance/standard keywords detected
- **Research**: Uses web search, URL fetch, MCP tools (whatever is available) to get latest requirements

## How Extensions Work

1. During Workflow Planning, AI scans this directory for extensions
2. Extensions are suggested based on project characteristics
3. User enables/disables as needed
4. Enabled extensions inject content at their configured stages
5. Extensions ADD to core guidance (never replace)
6. Priority determines application order (lower = earlier)

## Creating Extensions

Use the `extension-generator` skill to create extensions automatically, or create manually with: `rule-manifest.yaml` (required) + content `.md` files per stage.
