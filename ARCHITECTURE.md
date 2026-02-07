# Claude Skills Architecture & Flow

This document explains how Claude Skills work, their architecture, and the flow of execution across different platforms.

## Table of Contents

- [Overview](#overview)
- [Repository Structure](#repository-structure)
- [How Claude Skills Work](#how-claude-skills-work)
- [Skill Anatomy](#skill-anatomy)
- [Execution Flow](#execution-flow)
- [Platform-Specific Behavior](#platform-specific-behavior)
- [Integration with External Services](#integration-with-external-services)
- [Best Practices](#best-practices)

## Overview

The **awesome-claude-skills** repository is a curated collection of practical Claude Skills that extend Claude's capabilities across multiple platforms:

- **Claude.ai** - Web interface for conversational AI
- **Claude Code** - CLI tool for development workflows
- **Claude API** - Programmatic access for custom integrations

### What This Repository Contains

```
awesome-claude-skills/
├── README.md                    # Main documentation and skill catalog
├── SETUP.md                     # Installation and setup instructions
├── ARCHITECTURE.md              # This file - technical architecture
├── CONTRIBUTING.md              # Guidelines for contributing skills
├── connect/                     # Composio integration for app connectivity
├── connect-apps/               # Individual app connection skills
├── connect-apps-plugin/        # Plugin for Composio integration
├── template-skill/             # Template for creating new skills
├── skill-creator/              # Meta-skill for creating skills
└── [100+ individual skills]/   # Organized skill directories
```

### Skill Categories

Skills are organized into functional categories:

1. **Document Processing** - docx, pdf, pptx, xlsx manipulation
2. **Development & Code Tools** - MCP builders, testing, automation
3. **Data & Analysis** - CSV analysis, research tools
4. **Business & Marketing** - CRM, lead generation, branding
5. **Communication & Writing** - Email, Slack, content creation
6. **Creative & Media** - Image enhancement, video tools
7. **Productivity & Organization** - File organization, task management
8. **Collaboration & Project Management** - Jira, Linear, Asana
9. **Security & Systems** - Monitoring, incident response
10. **App Automation** - Composio-powered integrations

## Repository Structure

### Individual Skill Structure

Each skill follows a standardized directory structure:

```
skill-name/
├── SKILL.md              # Required: Main skill definition
├── scripts/              # Optional: Executable code
│   ├── helper.py
│   └── process.sh
├── references/           # Optional: Documentation to load as needed
│   ├── api_docs.md
│   └── schema.json
└── assets/               # Optional: Files used in output
    ├── template.html
    └── logo.png
```

### Key Files Explained

#### SKILL.md (Required)

The heart of every skill. Contains:

```markdown
---
name: skill-name
description: When and why to use this skill
---

# Skill Name

## Instructions

[Detailed instructions for Claude on how to use this skill]

## Examples

[Example usage scenarios]
```

The YAML frontmatter is critical:
- **`name`**: Unique identifier for the skill
- **`description`**: Tells Claude when to activate this skill

#### scripts/ (Optional)

Executable code that Claude can run:
- Python scripts for data processing
- Bash scripts for system tasks
- Node.js for web automation
- Any other executable code

**When to use:**
- Task requires deterministic reliability
- Same code is rewritten repeatedly
- Performance-critical operations

**Benefits:**
- Token efficient (can execute without loading into context)
- Consistent, bug-free execution
- Reusable across skill invocations

#### references/ (Optional)

Documentation Claude loads on-demand:
- API documentation
- Database schemas
- Company policies
- Domain knowledge
- Detailed workflow guides

**When to use:**
- Large documentation that shouldn't always be in context
- Reference material needed only sometimes
- Information that changes independently of skill logic

**Benefits:**
- Keeps SKILL.md lean
- Loaded only when Claude determines it's needed
- Separates stable instructions from reference data

#### assets/ (Optional)

Files used in Claude's output:
- Templates (HTML, PowerPoint, etc.)
- Images and icons
- Boilerplate code
- Fonts and styling

**When to use:**
- Skill produces files that need templates
- Output requires specific assets
- Consistent branding or formatting needed

**Benefits:**
- Claude can copy/modify without loading into context
- Enables complex output generation
- Maintains consistency across invocations

## How Claude Skills Work

### Progressive Disclosure Loading System

Skills use a three-level loading system to manage context efficiently:

```
┌─────────────────────────────────────────────────────────────┐
│ Level 1: Metadata (Always in Context)                       │
│ - name: skill-name                                           │
│ - description: When to use this skill                        │
│ (~100 words)                                                 │
└─────────────────────────────────────────────────────────────┘
                          ↓
                 Skill triggers based on
                  user task/description
                          ↓
┌─────────────────────────────────────────────────────────────┐
│ Level 2: SKILL.md Body (Loaded when skill triggers)         │
│ - Detailed instructions                                      │
│ - Usage examples                                             │
│ - Workflow steps                                             │
│ (<5k words)                                                  │
└─────────────────────────────────────────────────────────────┘
                          ↓
                 Claude determines what
                  resources are needed
                          ↓
┌─────────────────────────────────────────────────────────────┐
│ Level 3: Bundled Resources (Loaded as needed)               │
│ - scripts/: Execute without loading (unlimited)              │
│ - references/: Load specific docs as needed                  │
│ - assets/: Copy/modify without loading into context         │
└─────────────────────────────────────────────────────────────┘
```

### Skill Activation Flow

```
User Input
    ↓
Claude analyzes user's request
    ↓
Matches request against all skill descriptions
    ↓
    ├─→ Match found → Load SKILL.md body
    │                      ↓
    │                  Execute instructions
    │                      ↓
    │                  Load resources as needed
    │                      ↓
    │                  Generate output
    │
    └─→ No match → Use general capabilities
```

## Skill Anatomy

### Complete Example: Meeting Insights Analyzer

Let's examine how a real skill is structured:

```
meeting-insights-analyzer/
└── SKILL.md
```

**Frontmatter:**
```yaml
---
name: meeting-insights-analyzer
description: Analyzes meeting transcripts and recordings to uncover behavioral 
patterns, communication insights, and actionable feedback. Identifies when you 
avoid conflict, use filler words, dominate conversations, or miss opportunities 
to listen. Perfect for professionals seeking to improve their communication 
and leadership skills.
---
```

**Body Structure:**
1. **Overview**: What the skill does
2. **When to Use**: Trigger scenarios
3. **What This Skill Does**: Capabilities list
4. **Instructions**: Step-by-step guide for Claude
5. **Examples**: Real usage scenarios
6. **Setup Tips**: User guidance

### Example: PDF Skill with Scripts

```
pdf-skill/
├── SKILL.md
└── scripts/
    ├── rotate_pdf.py
    ├── merge_pdfs.py
    └── extract_text.py
```

The SKILL.md references these scripts:
```markdown
## Rotating PDFs

To rotate a PDF, use the `scripts/rotate_pdf.py` script:

```bash
python scripts/rotate_pdf.py input.pdf output.pdf --angle 90
```
```

### Example: BigQuery Skill with References

```
bigquery-skill/
├── SKILL.md
└── references/
    ├── schema.md
    └── query_patterns.md
```

The SKILL.md guides Claude to load references:
```markdown
## Querying Data

Before writing queries:
1. Load `references/schema.md` to understand table structure
2. Reference `references/query_patterns.md` for common patterns
3. Write optimized SQL based on schema
```

## Execution Flow

### Flow Diagram: Claude.ai Web Interface

```
┌──────────────┐
│     User     │
│  Opens Chat  │
└──────┬───────┘
       │
       ↓
┌─────────────────────┐
│  Skills Loaded      │
│  (Metadata Only)    │
└──────┬──────────────┘
       │
       ↓
┌─────────────────────┐
│  User Types Prompt  │
│  "Analyze meeting   │
│   transcript"       │
└──────┬──────────────┘
       │
       ↓
┌──────────────────────────────┐
│  Claude Matches Description  │
│  to User Intent              │
└──────┬───────────────────────┘
       │
       ↓
┌──────────────────────────────┐
│  Load SKILL.md Body          │
│  (meeting-insights-analyzer) │
└──────┬───────────────────────┘
       │
       ↓
┌──────────────────────────────┐
│  Execute Instructions        │
│  1. Scan for transcript      │
│  2. Analyze patterns         │
│  3. Generate insights        │
└──────┬───────────────────────┘
       │
       ↓
┌──────────────────────────────┐
│  Present Results to User     │
└──────────────────────────────┘
```

### Flow Diagram: Claude Code CLI

```
┌──────────────────────┐
│   User Starts CLI    │
│   $ claude           │
└──────┬───────────────┘
       │
       ↓
┌─────────────────────────────────┐
│  Load Skills from               │
│  ~/.config/claude-code/skills/  │
└──────┬──────────────────────────┘
       │
       ↓
┌─────────────────────────────────┐
│  Index Skill Metadata           │
│  (names + descriptions)         │
└──────┬──────────────────────────┘
       │
       ↓
┌─────────────────────────────────┐
│  User Issues Command            │
│  "Generate changelog from git"  │
└──────┬──────────────────────────┘
       │
       ↓
┌─────────────────────────────────┐
│  Match to changelog-generator   │
└──────┬──────────────────────────┘
       │
       ↓
┌─────────────────────────────────┐
│  Load SKILL.md                  │
└──────┬──────────────────────────┘
       │
       ↓
┌─────────────────────────────────┐
│  Execute Git Commands           │
│  (via bash tool)                │
└──────┬──────────────────────────┘
       │
       ↓
┌─────────────────────────────────┐
│  Load references/ if needed     │
└──────┬──────────────────────────┘
       │
       ↓
┌─────────────────────────────────┐
│  Generate Changelog             │
│  Write to CHANGELOG.md          │
└──────┬──────────────────────────┘
       │
       ↓
┌─────────────────────────────────┐
│  Display Result                 │
└─────────────────────────────────┘
```

### Flow Diagram: Claude API

```
┌──────────────────────┐
│  Application Code    │
│  (Python/Node.js)    │
└──────┬───────────────┘
       │
       ↓
┌─────────────────────────────────┐
│  Create API Request             │
│  {                              │
│    model: "claude-3-5-sonnet",  │
│    skills: ["skill-id"],        │
│    messages: [...]              │
│  }                              │
└──────┬──────────────────────────┘
       │
       ↓
┌─────────────────────────────────┐
│  Claude API Receives Request    │
└──────┬──────────────────────────┘
       │
       ↓
┌─────────────────────────────────┐
│  Load Specified Skill(s)        │
│  - Fetch skill by ID            │
│  - Or use local skill path      │
└──────┬──────────────────────────┘
       │
       ↓
┌─────────────────────────────────┐
│  Process Message with Skill     │
│  Context                        │
└──────┬──────────────────────────┘
       │
       ↓
┌─────────────────────────────────┐
│  Execute Skill Instructions     │
└──────┬──────────────────────────┘
       │
       ↓
┌─────────────────────────────────┐
│  Return Response                │
│  { content: [...] }             │
└──────┬──────────────────────────┘
       │
       ↓
┌─────────────────────────────────┐
│  Application Processes Result   │
└─────────────────────────────────┘
```

## Platform-Specific Behavior

### Claude.ai

**Characteristics:**
- Skills stored in user's workspace
- Visual skill picker interface
- Skills can be enabled/disabled per conversation
- Automatic skill activation based on context

**Skill Location:**
- Cloud-based (stored in Anthropic's servers)
- Accessible from any device

**Best For:**
- Quick, ad-hoc tasks
- Non-technical users
- Collaborative work
- Portable access

### Claude Code

**Characteristics:**
- Skills stored locally on machine
- Command-line interface
- Direct file system access
- Can execute scripts and commands

**Skill Location:**
- `~/.config/claude-code/skills/` (Linux/Mac)
- `%APPDATA%\claude-code\skills\` (Windows)

**Best For:**
- Development workflows
- File manipulation
- Code generation and analysis
- System automation

### Claude API

**Characteristics:**
- Skills specified per API call
- Can use skill IDs or local skill paths
- Programmatic control
- Scalable for production use

**Skill Location:**
- Marketplace skills: Referenced by ID
- Custom skills: Uploaded or referenced by path

**Best For:**
- Production applications
- Custom integrations
- Automated workflows
- Enterprise deployments

## Integration with External Services

### Composio Integration Architecture

```
┌─────────────────┐
│  Claude         │
│  (Any Platform) │
└────────┬────────┘
         │
         │ Uses connect-apps skill
         ↓
┌─────────────────────────┐
│  Composio Plugin        │
│  (connect-apps-plugin)  │
└────────┬────────────────┘
         │
         │ API calls via Composio SDK
         ↓
┌─────────────────────────┐
│  Composio Platform      │
│  - Authentication       │
│  - API abstraction      │
│  - Action execution     │
└────────┬────────────────┘
         │
         │ Connects to
         ↓
┌──────────────────────────────────┐
│  External Services               │
│  ├─ Gmail                        │
│  ├─ Slack                        │
│  ├─ GitHub                       │
│  ├─ Jira                         │
│  └─ 1000+ other apps             │
└──────────────────────────────────┘
```

### Example: Send Email Flow

```
User: "Send an email to john@example.com"
         ↓
Claude: Identifies this requires Gmail
         ↓
Activates: connect-apps/gmail skill
         ↓
Calls: Composio API - gmail.send_email()
         ↓
Composio: Handles OAuth, API calls
         ↓
Gmail: Sends email
         ↓
Composio: Returns success/failure
         ↓
Claude: Confirms to user "Email sent!"
```

### App-Specific Skills

The repository includes 50+ app-specific automation skills:

```
app-automation-skills/
├── gmail-automation/
├── slack-automation/
├── github-automation/
├── jira-automation/
├── salesforce-automation/
└── ...
```

Each skill:
1. Defines the app's capabilities
2. Documents authentication requirements
3. Provides workflow templates
4. Includes example usage

## Best Practices

### For Skill Users

1. **Start with Marketplace Skills**: Try existing skills before creating custom ones
2. **Read Skill Descriptions**: Understand when to use each skill
3. **Combine Skills**: Multiple skills can work together
4. **Explicit Invocation**: Mention skill name if auto-activation doesn't work
5. **Provide Context**: Give Claude enough information to choose the right skill

### For Skill Creators

1. **Write Clear Descriptions**: The description determines when skill activates
2. **Keep SKILL.md Focused**: Move detailed docs to references/
3. **Use Scripts for Repetition**: Don't make Claude rewrite the same code
4. **Include Examples**: Show real-world usage scenarios
5. **Test Across Platforms**: Verify skill works on Claude.ai, Code, and API
6. **Document Dependencies**: List any required tools or packages
7. **Version Control**: Track skill changes and compatibility

### Skill Design Patterns

#### Pattern 1: Analysis Skills
- Load data from user
- Apply analysis framework
- Generate structured insights
- Provide actionable recommendations

Example: meeting-insights-analyzer, developer-growth-analysis

#### Pattern 2: Generation Skills
- Gather requirements
- Load templates from assets/
- Fill in templates
- Output formatted result

Example: artifacts-builder, canvas-design, tailored-resume-generator

#### Pattern 3: Automation Skills
- Connect to external service (via Composio)
- Execute actions based on user intent
- Handle errors and retries
- Report results

Example: gmail-automation, slack-automation, github-automation

#### Pattern 4: Transformation Skills
- Accept input files
- Run scripts/ to transform
- Produce output files
- Validate results

Example: document-skills (pdf, docx, xlsx)

## Advanced Topics

### Skill Chaining

Skills can work together in sequence:

```
User: "Analyze my meeting transcripts and send insights to my team on Slack"
         ↓
Skill 1: meeting-insights-analyzer
         ├─ Loads transcripts
         ├─ Analyzes patterns
         └─ Generates insights document
         ↓
Skill 2: slack-automation
         ├─ Formats insights for Slack
         ├─ Connects via Composio
         └─ Posts to channel
```

### Context Management

Skills manage context efficiently:

- **Metadata**: Always loaded (~100 words per skill)
- **Instructions**: Loaded when triggered (<5k words)
- **References**: Loaded on-demand (unlimited with smart loading)
- **Scripts**: Executed without loading (unlimited)

This allows hundreds of skills to be available without overwhelming context.

### Security Considerations

1. **Local Skills**: Can access your file system (use trusted skills only)
2. **API Keys**: Store securely, never commit to version control
3. **OAuth**: Composio handles securely, but review permissions
4. **Script Execution**: Scripts run with your user permissions
5. **Data Privacy**: Skills may send data to Anthropic (read privacy policy)

## Contributing

To add a skill to this repository:

1. Follow the [template-skill](./template-skill/) structure
2. Read [CONTRIBUTING.md](./CONTRIBUTING.md) for guidelines
3. Test your skill across platforms
4. Submit a pull request with:
   - Skill directory with SKILL.md
   - Updated README.md with skill entry
   - Real-world use case description

## Resources

> **Note:** External documentation URLs may change. Visit [anthropic.com](https://www.anthropic.com) for the latest official documentation.

- **Official Docs**: [Claude Skills Overview](https://www.anthropic.com/news/skills)
- **User Guide**: [Using Skills in Claude](https://support.claude.com/en/articles/12512180-using-skills-in-claude)
- **Creating Skills**: [Creating Custom Skills](https://support.claude.com/en/articles/12512198-creating-custom-skills)
- **API Guide**: See Anthropic API documentation for Skills integration
- **Engineering Blog**: Visit Anthropic blog for technical deep dives on Agent Skills

## Conclusion

Claude Skills transform Claude from a general-purpose AI into a specialized agent equipped with domain-specific knowledge and capabilities. By understanding the architecture and flow, you can:

- **Use skills effectively** across all Claude platforms
- **Create custom skills** tailored to your workflows
- **Integrate external services** via Composio
- **Build production applications** with the Claude API

The modular, progressive disclosure design ensures skills remain efficient and scalable, even as your skill library grows to hundreds of specialized capabilities.
