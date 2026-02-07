# AGENTS.md - Agentic Coding Guidelines

> For AI agents working in the BMAD (Build-Measure-Architect-Deploy) workflow system

## 规则说明
- 所有Thinking过程均使用中文进行显示
- 所有回复均使用中文进行显示
- 所有生成的md文件，均需要使用中文进行书写


## Project Overview

This is a BMAD workflow and agent system - a knowledge-driven framework for software development workflows using markdown-based agents and YAML configurations. The codebase contains:

- **Agents**: Markdown files with XML-based definitions and persona definitions
- **Workflows**: Step-based procedural documents in `_bmad/[module]/workflows/`
- **Configuration**: YAML files defining module settings and manifest
- **Tasks**: Executable procedures loaded by agents

## Project Structure

```
_bmad/
├── _config/
│   ├── manifest.yaml          # Module registry and installation info
│   └── agents/               # Agent customization files (*.customize.yaml)
├── _memory/                  # Persistent memory for agents
├── core/                     # Core BMAD system
│   ├── config.yaml
│   ├── agents/
│   └── workflows/
├── [module]/                 # Feature modules (bmm, bmb, cis, gds, tea)
│   ├── config.yaml
│   ├── agents/
│   ├── workflows/
│   │   └── [workflow-name]/
│   │       ├── workflow.md
│   │       ├── data/         # CSV data files
│   │       ├── templates/    # Markdown templates
│   │       └── steps/        # Step files (step-01-name.md)
│   └── teams/                # Team definitions
└── [module]-output/         # Generated outputs (gitignored)

.opencode/
├── package.json
└── command/                 # CLI command definitions (*.md)
```

## Build/Lint/Test Commands

This project is documentation-heavy with no traditional build step:

```bash
# No build process required - markdown/YAML based system
# Use filesystem operations to validate structure

# Validate manifest syntax
cat _bmad/_config/manifest.yaml | head -20

# Check for broken references in workflow files
grep -r "{project-root}" _bmad/ --include="*.md" | head -10
```

## Code Style Guidelines

### Markdown Files

**Frontmatter (Required for all workflow/agent files):**
```yaml
---
name: 'descriptive-name'
description: 'Brief description of purpose'
---
```

**File Naming:**
- Use lowercase with hyphens: `step-01-setup.md`, `create-story.md`
- Step files: `step-XX-descriptive-name.md` (01, 02, 03...)
- Agent files: `[module]-[role].md` (e.g., `bmm-pm.md`)
- Command files: `bmad-[module]-[action].md`

**Content Structure:**
- Use ATX-style headers (`#`, `##`)
- One blank line between sections
- Placeholders use `{variable-name}` syntax from config
- Never include actual values in place of placeholders

### YAML Files

**Configuration Files:**
```yaml
# Top-level comment with file purpose
key: value
nested:
  child_key: value
list:
  - item1
  - item2
```

**Agent Customize Files:**
```yaml
persona:
  description: "..."
  skills:
    - skill_name
system_prompt: |
  Multi-line content
  with proper indentation
```

### XML in Markdown (Agent Definitions)

```xml
<agent>
  <activation>
    <step n="1">First activation step</step>
  </activation>
  <persona>
    <identity>...</identity>
  </persona>
  <menu>
    <item cmd="keyword" handler="attribute">Description</item>
  </menu>
  <rules>
    <rule>Behavior constraint</rule>
  </rules>
</agent>
```

## Naming Conventions

- **Modules**: 3-letter lowercase (bmm, bmb, cis, gds, tea, core)
- **Workflows**: kebab-case (create-story, code-review, sprint-planning)
- **Agents**: [module]-[role] pattern (bmm-pm, gds-game-dev)
- **Steps**: Sequential with leading zeros (step-01, step-02)
- **Variables**: snake_case in configs, used as {variable_name} in content

## Error Handling

- Always check file existence before reading
- Use relative paths from {project-root}
- Validate YAML syntax before saving
- Never create files outside _bmad/ or .opencode/
- If workflow step fails, report exact location (file:line)

## Workflow Development Rules

1. **Step Isolation**: Each step file must be completely self-contained
2. **Sequential Loading**: Never load multiple step files at once
3. **State Tracking**: Update frontmatter `stepsCompleted` array after each step
4. **Template Usage**: Reference templates with relative paths from workflow directory
5. **Data Files**: Use CSV format in data/ folder for tabular information

## Configuration Values

Common placeholders from config files:
- `{user_name}` - Current user
- `{communication_language}` - Preferred language for communication
- `{document_output_language}` - Language for generated documents
- `{output_folder}` - Base path for generated outputs
- `{project-root}` - Repository root directory

## Testing Guidelines

For workflow testing:
1. Read entire file before making changes
2. Verify frontmatter is valid YAML
3. Check all placeholder variables exist in config
4. Ensure step files follow naming convention
5. Validate XML structure in agent files

## Critical Constraints

- NEVER modify files in .opencode/node_modules/
- NEVER skip workflow steps
- NEVER hardcode paths - always use placeholders
- NEVER delete _memory/ contents
- ALWAYS append to outputs, never overwrite in multi-step workflows
