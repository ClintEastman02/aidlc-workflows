# Available Extensions

Extensions add domain-specific rules to AI-DLC phases. They are discovered automatically during Workflow Planning.

## Installed Extensions

### extension-generator (v2.0.0)
IDE-agnostic skill that researches compliance standards using available tools (web search, fetch, MCP servers) then generates a complete extension folder. Works across all supported AI-DLC platforms.
- **Category**: process | **Priority**: 5
- **Stages**: Workflow Planning (researches + generates extensions covering all stages)
- **Auto-suggested**: When compliance/standard keywords detected
- **Research**: Uses web search, URL fetch, MCP tools (whatever is available) to get latest requirements

### user-provided-rules (v1.0.0)
Points to user's existing rule folders. AI scans, classifies by phase, and injects at each stage.
- **Category**: quality | **Priority**: 10
- **Stages**: All Inception + Construction stages
- **Auto-suggested**: Always available, user opts in with folder paths

## How Extensions Work

1. During Workflow Planning, AI scans this directory for extensions
2. Extensions are suggested based on project characteristics
3. User enables/disables as needed
4. Enabled extensions inject content at their configured stages
5. Extensions ADD to core guidance (never replace)
6. Priority determines application order (lower = earlier)

## Creating Extensions

Each extension needs: `rule-manifest.yaml` (required) + content `.md` files per stage.
See `EXTENSION-AUTHORING-GUIDE.md` in the repository root for full documentation.
