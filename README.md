# Claude Skills Library

A curated collection of skills that extend Claude's capabilities for specialized tasks and workflows.

## What are Claude Skills?

Skills are modular packages that give Claude specialized knowledge and procedural instructions for specific domains. Think of them as "expert guides" that help Claude excel at tasks like product management, document creation, data analysis, and more.

## Available Skills

### Product Manager
**Location:** `product-manager/`  
**Description:** AI Product Manager that conducts discovery interviews, explores codebases, and generates comprehensive PRDs.

**Features:**
- 5-phase discovery interview process
- Codebase exploration (in VS Code)
- Interactive UI mockup creation (in Web/Desktop)
- Comprehensive PRD generation
- Multi-state component examples

**Use when:**
- Creating product requirements documents
- Defining new features
- Documenting technical specifications

---

## Installation

Skills are installed in different locations depending on your environment:

### 🌐 Claude Web (claude.ai)

**Not Currently Supported**
Download the skill and compress to zip.  
Go to settings -> Capabilities -> Click Upload Skill, drag and drop the zipped skill, then enable.

---

### 🖥️ Claude Desktop App

**Location:**
```bash
# macOS
~/Library/Application Support/Claude/skills/

# Windows
%APPDATA%\Claude\skills\

# Linux
~/.config/Claude/skills/
```

**Installation Steps:**

1. **Download the skill archive** (e.g., `product-manager-skill-v2.tar.gz`)

2. **Extract to skills directory:**

   **macOS/Linux:**
   ```bash
   cd ~/Library/Application\ Support/Claude/skills/
   tar -xzf ~/Downloads/product-manager-skill-v2.tar.gz
   ```

   **Windows (PowerShell):**
   ```powershell
   cd $env:APPDATA\Claude\skills
   tar -xzf "$env:USERPROFILE\Downloads\product-manager-skill-v2.tar.gz"
   ```

3. **Verify installation:**
   ```bash
   ls product-manager/
   # Should show: SKILL.md and references/
   ```

4. **Restart Claude Desktop** if already open

---

### 💻 VS Code Extension (Claude Code)

**Location:**
```bash
# All platforms
~/.claude/skills/
# or
%USERPROFILE%\.claude\skills\  (Windows)
```

**Installation Steps:**

1. **Create skills directory** (if it doesn't exist):
   ```bash
   mkdir -p ~/.claude/skills
   ```

2. **Extract skill:**
   ```bash
   cd ~/.claude/skills
   tar -xzf ~/Downloads/product-manager-skill-v2.tar.gz
   ```

3. **Verify installation:**
   ```bash
   ls product-manager/
   # Should show: SKILL.md and references/
   ```

4. **Restart VS Code** to load the skill

---

### 🐧 WSL Environment (Windows Subsystem for Linux)

**Location:**
```bash
# Inside WSL
/mnt/skills/user/

# From Windows File Explorer
\\wsl$\Ubuntu\mnt\skills\user\
```

**Installation Steps:**

**From WSL Terminal:**
```bash
cd /mnt/skills/user/
tar -xzf /mnt/c/Users/YourUsername/Downloads/product-manager-skill-v2.tar.gz
```

**From Windows PowerShell:**
```powershell
# Navigate to WSL skills directory
cd \\wsl$\Ubuntu\mnt\skills\user\

# Extract (requires tar.exe on Windows)
tar -xzf "C:\Users\YourUsername\Downloads\product-manager-skill-v2.tar.gz"
```

**From Windows File Explorer:**
1. Open `\\wsl$\Ubuntu\mnt\skills\user\` in File Explorer
2. Right-click the `.tar.gz` file → Extract All
3. Move the extracted folder to the WSL skills directory

---

## Usage

### Triggering a Skill

Skills activate when you use specific phrases or keywords:

**Product Manager Skill:**
```
"Help me write a PRD for a notification system"
"Act as a product manager and define requirements"
"Create a PRD for user authentication"
```

**General Pattern:**
- Use natural language that matches the skill's domain
- Mention key terms from the skill description
- Start with action verbs: "Help me...", "Create...", "Generate..."

### Environment-Specific Behavior

Skills can adapt based on where you're using Claude:

| Environment | Behavior |
|-------------|----------|
| **VS Code** | Accesses codebase, explores files, suggests integrations |
| **Desktop** | Creates artifacts, interactive mockups, downloads |
| **Web** | Creates artifacts, interactive mockups *(when supported)* |

**Example - Product Manager Skill:**

**In VS Code:**
```
"Help me write a PRD for API rate limiting"
→ Explores your codebase structure
→ Identifies tech stack and patterns
→ Generates PRD with technical integration details
```

**In Desktop:**
```
"Help me write a PRD for a settings page"
→ Asks about UI requirements
→ Creates interactive UI mockups (React components)
→ Generates PRD with mockup links
```

---

## Skill Structure

Each skill follows this standard structure:

```
skill-name/
├── SKILL.md              # Main instructions (required)
│   ├── YAML frontmatter  # Name and description
│   └── Markdown body     # Workflows and guidelines
│
└── references/           # Optional detailed docs
    ├── reference1.md     # Loaded as needed
    └── reference2.md     # Domain-specific guides
```

---

## Verifying Installation

### Check Skill Files

```bash
# Desktop (macOS)
ls ~/Library/Application\ Support/Claude/skills/product-manager/

# Desktop (Windows)
dir %APPDATA%\Claude\skills\product-manager

# VS Code
ls ~/.claude/skills/product-manager/

# WSL
ls /mnt/skills/user/product-manager/
```

**Expected output:**
```
SKILL.md
references/
```

### Test the Skill

1. Open Claude in your environment
2. Start a new conversation
3. Use a trigger phrase (e.g., "Help me write a PRD for...")
4. Claude should acknowledge and use the skill

**Signs the skill is working:**
- Claude mentions skill-specific terminology
- Follows the skill's workflow/process
- References skill documentation

---

## Troubleshooting

### Skill Not Loading

**Problem:** Claude doesn't seem to use the skill

**Solutions:**
1. **Verify file location:**
   ```bash
   # Check the skill is in the right directory
   ls <skills-directory>/product-manager/SKILL.md
   ```

2. **Check YAML frontmatter:**
   ```bash
   head -5 <skills-directory>/product-manager/SKILL.md
   ```
   Should show:
   ```yaml
   ---
   name: skill-name
   description: ...
   ---
   ```

3. **Restart Claude:**
   - Desktop: Quit and reopen app
   - VS Code: Reload window (Cmd/Ctrl + Shift + P → "Reload Window")

4. **Try explicit trigger:**
   ```
   "Use the product-manager skill to help me..."
   ```

5. **Check permissions:**
   ```bash
   chmod -R 644 <skills-directory>/product-manager/
   ```

### File Not Found Errors

**Problem:** Claude can't find reference files

**Solution:**
```bash
# Verify references directory exists
ls <skills-directory>/product-manager/references/

# Fix permissions if needed
chmod -R 644 <skills-directory>/product-manager/references/
```

### Windows Path Issues

**Problem:** Spaces in path cause errors

**Solution:** Always use quotes:
```powershell
# Wrong
tar -xzf C:\Users\Will Marshall\Downloads\file.tar.gz

# Right
tar -xzf "C:\Users\Will Marshall\Downloads\file.tar.gz"
```

---

## Updating Skills

### Replace Entire Skill

```bash
# Backup existing
mv product-manager/ product-manager.backup/

# Extract new version
tar -xzf product-manager-skill-v2.tar.gz

# Restart Claude
```

### Update Individual Files

```bash
# Keep custom modifications
cp product-manager/SKILL.md product-manager/SKILL.md.backup

# Replace specific file
cp new-SKILL.md product-manager/SKILL.md

# Restart Claude
```

---

## Uninstalling Skills

```bash
# Remove entire skill
rm -rf <skills-directory>/product-manager/

# Or move to backup
mv <skills-directory>/product-manager/ ~/skill-backups/

# Restart Claude
```

---

## FAQ

### Q: Can I use the same skill in multiple environments?

**A:** Yes! Install the skill in each environment's skills directory. The skill can adapt its behavior based on available capabilities.

### Q: How many skills can I install?

**A:** No hard limit, but Claude can access ~100+ skills efficiently. Too many may slow skill selection.

### Q: Do skills work offline?

**A:** Skills are local files, but Claude needs internet to process requests. Once loaded, skill instructions are cached.

### Q: Can I modify existing skills?

**A:** Yes! Skills are plain markdown files. Edit SKILL.md to customize behavior. Keep backups before modifying.

### Q: How do I share skills with my team?

**A:** Package as `.tar.gz` or share the skill directory. Recipients install in their skills directory.

### Q: Can skills access my data?

**A:** Skills are instructions for Claude, not executable code. They guide Claude's behavior but don't independently access files.

### Q: What if two skills conflict?

**A:** Claude chooses the most relevant skill based on your request. Use explicit triggers ("Use skill-name to...") to force a specific skill.

### Q: Are skills sandboxed?

**A:** In VS Code/WSL, skills can guide Claude to execute code. In Desktop, skills create artifacts but don't execute code.

---

## Resources

### Official Documentation
- [Anthropic Skills Documentation](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview)

---

## Contributing

Want to add a skill to this library?

1. **Fork this repository**
2. **Create your skill** following the structure above
3. **Test thoroughly** in multiple environments
4. **Document usage** with clear examples
5. **Submit pull request** with:
   - Skill directory
   - README update
   - Example usage
   - Test cases

---

## License

Skills in this repository are provided as-is for use with Claude. Individual skills may have their own licenses - check each skill's directory.

---

## Changelog

### 2024-10-24
- Added Product Manager skill v2.0
- Added UI mockup creation capability
- Updated installation instructions for all environments

---

**Note:** This is a community-maintained library. Skills are not officially supported by Anthropic. Use at your own discretion and always review skill contents before installation.
