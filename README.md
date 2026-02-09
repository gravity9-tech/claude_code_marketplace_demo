# Pandora Marketplace

Organisation-level plugins for Claude Code.

## Plugins

| Plugin | Description |
|--------|-------------|
| **feature-lifecycle** | Jira planning, TDD implementation, code review, and automated merge |
| **deepwiki** | Generate documentation from codebases with ASCII diagrams and auto-synced Claude Code skills |

## Installation

### Add the Marketplace

From GitHub:

```
/plugin marketplace add gravity9-tech/claude-code-marketplace-demo
```

From a local clone:

```
/plugin marketplace add ./path/to/claude-code-marketplace-demo
```

Or point directly to the `marketplace.json` file:

```
/plugin marketplace add https://github.com/gravity9-tech/claude_code_marketplace_demo/blob/main/marketplace.json
```

### Install a Plugin

```
/plugin install feature-lifecycle@pandora-marketplace
/plugin install deepwiki@pandora-marketplace
```

You can also use the interactive plugin browser:

```
/plugin
```

This opens a tabbed UI where you can browse the **Discover** tab to see available plugins from all configured marketplaces.

### Manage Plugins

```
/plugin marketplace list          # List configured marketplaces
/plugin marketplace update        # Refresh plugin listings
/plugin disable <plugin>          # Disable without uninstalling
/plugin enable <plugin>           # Re-enable
/plugin uninstall <plugin>        # Remove completely
```

### Team Setup

To have team members automatically prompted to add this marketplace, add to your project's `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "pandora-marketplace": {
      "source": {
        "source": "github",
        "repo": "<owner>/claude-code-marketplace-demo"
      }
    }
  },
  "enabledPlugins": {
    "feature-lifecycle@pandora-marketplace": true,
    "deepwiki@pandora-marketplace": true
  }
}
```

## Quick Start

### DeepWiki

```
/deepwiki:init ./ ./wiki
/deepwiki:sync ./wiki
/deepwiki:skills list
```

### Feature Lifecycle

Requires the Atlassian MCP server for Jira integration.

```
/implement-feature Add user authentication with OAuth2 PROJ
```

## Plugin Components

### DeepWiki

- **Commands:** `init`, `sync`, `skills`
- **Agents:** `deepwiki-planner`, `deepwiki-doc-generator`, `deepwiki-skill-generator`
- **Skills:** generating-ascii-diagrams, evidence-citation, codebase-analysis-patterns, documentation-structure-patterns, documentation-quality-checklist, planner-output-templates, doc-generator-output-templates

### Feature Lifecycle

- **Commands:** `implement-feature`
- **Agents:** `planner`, `tdd-implementer`, `code-reviewer`
- **Skills:** create-ticket, expand-ticket, tdd-workflow, create-branch, commit-and-merge
- **MCP:** Atlassian (Jira)
