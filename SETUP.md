# Setup Guide for Claude Skills

This guide will help you get started with Claude Skills across different platforms.

## Table of Contents

- [What Are Claude Skills?](#what-are-claude-skills)
- [Prerequisites](#prerequisites)
- [Installation & Setup](#installation--setup)
  - [For Claude.ai Web Interface](#for-claudeai-web-interface)
  - [For Claude Code (CLI)](#for-claude-code-cli)
  - [For Claude API](#for-claude-api)
- [Using Skills with Composio](#using-skills-with-composio)
- [Verifying Your Setup](#verifying-your-setup)
- [Troubleshooting](#troubleshooting)

## What Are Claude Skills?

Claude Skills are modular, self-contained packages that extend Claude's capabilities by providing:

- **Specialized workflows** - Multi-step procedures for specific domains
- **Tool integrations** - Instructions for working with specific file formats or APIs
- **Domain expertise** - Company-specific knowledge, schemas, business logic
- **Bundled resources** - Scripts, references, and assets for complex tasks

Think of skills as "onboarding guides" that transform Claude from a general-purpose AI into a specialized agent equipped with procedural knowledge for your specific needs.

## Prerequisites

Before you begin, make sure you have:

- **Claude.ai Account**: Sign up at [claude.ai](https://claude.ai) (free or paid tier)
- **Claude Code**: For CLI usage, install Claude Code (available via Anthropic's official channels)
- **API Access**: For programmatic usage, obtain an API key from [console.anthropic.com](https://console.anthropic.com)

## Installation & Setup

### For Claude.ai Web Interface

The easiest way to use skills is through the Claude.ai web interface:

#### Step 1: Access Skills
1. Log in to [claude.ai](https://claude.ai)
2. Look for the skills icon (🧩) in your chat interface
3. Click on it to open the skills panel

#### Step 2: Add Skills
You have two options:

**Option A: From Marketplace**
1. Click "Browse Marketplace" or "Add Skill"
2. Browse the available skills
3. Click on a skill to view its details
4. Click "Add to Workspace" or "Install"

**Option B: Upload Custom Skills**
1. Download or create a skill (must be a `.zip` file containing a `SKILL.md`)
2. Click "Upload Custom Skill"
3. Select the skill zip file
4. The skill will be added to your workspace

#### Step 3: Use Skills
- Skills automatically activate when relevant to your task
- You can also manually invoke skills by mentioning them in your prompt
- Example: "Use the meeting-insights-analyzer skill to analyze this transcript"

### For Claude Code (CLI)

Claude Code allows you to use skills directly from your terminal:

#### Step 1: Install Claude Code
```bash
# Download and install Claude Code from official website
# Follow platform-specific instructions at anthropic.com
```

#### Step 2: Create Skills Directory
```bash
# Create the skills directory if it doesn't exist
mkdir -p ~/.config/claude-code/skills/
```

#### Step 3: Add Skills
You can add skills by:

**Option A: Clone from this repository**
```bash
cd ~/.config/claude-code/skills/
git clone https://github.com/sivaraj-enverus/awesome-claude-skills.git
# Or copy individual skill folders
```

**Option B: Copy individual skills**
```bash
# Copy a specific skill
cp -r /path/to/skill-name ~/.config/claude-code/skills/
```

**Option C: Unzip downloaded skills**
```bash
# Extract skill zip file
unzip skill-name.zip -d ~/.config/claude-code/skills/
```

#### Step 4: Verify Installation
```bash
# Check that the skill has proper structure
ls ~/.config/claude-code/skills/skill-name/
# Should show: SKILL.md and optionally scripts/, references/, assets/

# View skill metadata
head ~/.config/claude-code/skills/skill-name/SKILL.md
# Should show YAML frontmatter with name and description
```

#### Step 5: Start Claude Code
```bash
# Launch Claude Code
claude

# Skills load automatically and activate when relevant
```

### For Claude API

Use skills programmatically through the Claude API:

#### Step 1: Install SDK
```bash
# Python
pip install anthropic

# Node.js
npm install @anthropic-ai/sdk
```

#### Step 2: Get API Key
1. Visit [console.anthropic.com](https://console.anthropic.com)
2. Navigate to API Keys
3. Create a new API key
4. Store it securely (e.g., in environment variable)

#### Step 3: Use Skills in Code

**Python Example:**
```python
import anthropic
import os

# Initialize client
client = anthropic.Anthropic(
    api_key=os.environ.get("ANTHROPIC_API_KEY")
)

# Create message with skill
response = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    skills=["skill-id-or-path"],  # Skill ID from marketplace or path to local skill
    max_tokens=1024,
    messages=[
        {
            "role": "user",
            "content": "Your prompt that should trigger the skill"
        }
    ]
)

print(response.content[0].text)
```

**Node.js Example:**
```javascript
import Anthropic from '@anthropic-ai/sdk';

const client = new Anthropic({
  apiKey: process.env.ANTHROPIC_API_KEY,
});

const response = await client.messages.create({
  model: 'claude-3-5-sonnet-20241022',
  skills: ['skill-id-or-path'],
  max_tokens: 1024,
  messages: [
    {
      role: 'user',
      content: 'Your prompt that should trigger the skill',
    },
  ],
});

console.log(response.content[0].text);
```

## Using Skills with Composio

Many skills in this repository integrate with external apps via [Composio](https://composio.dev), allowing Claude to take real actions like sending emails, creating issues, and posting to Slack.

### Quick Setup

#### Step 1: Install Composio Plugin
```bash
# For Claude Code
claude --plugin-dir ./connect-apps-plugin
```

#### Step 2: Configure Composio
```bash
# Start Claude Code
claude

# Run setup command
/connect-apps:setup
```

#### Step 3: Get API Key
1. Visit [platform.composio.dev](https://platform.composio.dev/?utm_source=Github&utm_content=AwesomeSkills)
2. Sign up for a free account
3. Copy your API key
4. Paste it when prompted in Claude Code

#### Step 4: Connect Apps
```bash
# Restart Claude Code
exit
claude

# Connect specific apps (e.g., Gmail, Slack, GitHub)
# Claude will guide you through OAuth flow
```

#### Step 5: Test Integration
Ask Claude to perform an action:
```
Send me a test email to verify the Gmail connection
```

If you receive the email, the integration is working!

### Supported Apps
Composio supports 1000+ apps including:
- Communication: Gmail, Outlook, Slack, Discord, Telegram
- Project Management: Jira, Linear, Asana, Trello, Monday
- Development: GitHub, GitLab, Bitbucket
- CRM: Salesforce, HubSpot, Pipedrive
- Marketing: Mailchimp, SendGrid, ActiveCampaign
- And many more...

See [all supported apps](https://composio.dev/toolkits)

## Verifying Your Setup

### Test 1: Check Skills are Loaded
**Claude.ai:**
- Click skills icon (🧩)
- Verify your skills appear in the list

**Claude Code:**
```bash
# Start Claude
claude

# Ask about available skills
"What skills do you have access to?"
```

### Test 2: Use a Basic Skill
Try using a simple skill:
```
Use the changelog-generator skill to create a changelog from recent commits
```

### Test 3: Verify Composio Connection (if applicable)
```
List the apps I'm currently connected to through Composio
```

## Troubleshooting

### Skills Not Loading

**Problem:** Skills don't appear or aren't being used

**Solutions:**
1. **Verify directory structure:**
   ```bash
   # Ensure SKILL.md exists
   ls ~/.config/claude-code/skills/skill-name/SKILL.md
   ```

2. **Check YAML frontmatter:**
   ```bash
   # First few lines should have valid YAML
   head -10 ~/.config/claude-code/skills/skill-name/SKILL.md
   ```
   
   Should look like:
   ```yaml
   ---
   name: skill-name
   description: Description of the skill
   ---
   ```

3. **Restart Claude Code:**
   ```bash
   exit
   claude
   ```

### Composio Connection Issues

**Problem:** Can't connect to apps through Composio

**Solutions:**
1. **Verify API key:**
   ```bash
   # Re-run setup
   /connect-apps:setup
   ```

2. **Check connection status:**
   Ask Claude: "What's the status of my Composio connections?"

3. **Reconnect app:**
   - Disconnect and reconnect the specific app
   - Check OAuth permissions are granted

### Skill Not Activating

**Problem:** Skill doesn't activate when expected

**Solutions:**
1. **Explicitly invoke the skill:**
   ```
   Use the [skill-name] skill to [your task]
   ```

2. **Check skill description:**
   - Skill activates based on its `description` in YAML frontmatter
   - Make sure your prompt matches the skill's intended use case

3. **Verify skill is relevant:**
   - Skills auto-activate only when relevant to the task
   - Some tasks may not match any skill's criteria

### Permission Issues

**Problem:** Can't create skills directory or copy files

**Solutions:**
```bash
# Check directory permissions
ls -la ~/.config/claude-code/

# Create directory with proper permissions
mkdir -p ~/.config/claude-code/skills/
chmod 755 ~/.config/claude-code/skills/

# If still having issues, try with sudo (use cautiously)
sudo mkdir -p ~/.config/claude-code/skills/
sudo chown $USER:$USER ~/.config/claude-code/skills/
```

### API Issues

**Problem:** API calls failing or returning errors

**Solutions:**
1. **Verify API key:**
   ```bash
   # Check environment variable
   echo $ANTHROPIC_API_KEY
   ```

2. **Check API usage limits:**
   - Visit [console.anthropic.com](https://console.anthropic.com)
   - Review usage dashboard

3. **Update SDK:**
   ```bash
   # Python
   pip install --upgrade anthropic
   
   # Node.js
   npm update @anthropic-ai/sdk
   ```

## Next Steps

Now that you have Claude Skills set up:

1. **Explore Skills:** Browse the [skills directory](./README.md#skills) to find skills relevant to your work
2. **Create Custom Skills:** Follow the [CONTRIBUTING.md](./CONTRIBUTING.md) guide to create your own
3. **Join Community:** Connect with other users on [Discord](https://discord.com/invite/composio)
4. **Read Architecture:** Understand how skills work by reading [ARCHITECTURE.md](./ARCHITECTURE.md)

## Getting Help

**For Claude Skills:**
- **Documentation:** [Official Skills Guide](https://support.claude.com/en/articles/12512180-using-skills-in-claude)
- **Community:** [Anthropic Community](https://community.anthropic.com)
- **Issues:** [Report issues on GitHub](https://github.com/sivaraj-enverus/awesome-claude-skills/issues)

**For Composio Integration:**
- **Discord:** [Join Composio Discord](https://discord.com/invite/composio)
- **Email:** support@composio.dev
