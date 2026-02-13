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
- User flow diagram generation with Mermaid.js
- Comprehensive PRD generation
- Multi-state component examples

**Use when:**
- Creating product requirements documents
- Defining new features
- Documenting technical specifications
- Visualizing user journeys and workflows

---

### Issue Creator
**Location:** `issue-creator/`
**Description:** Creates well-structured development issues from PRDs, feature requests, bug reports, or technical specifications.

**Features:**
- Converts PRDs and requirements docs into actionable issues
- Generates BDD-ready acceptance criteria
- Comprehensive testing requirements (unit, integration, E2E)
- Intelligent dependency ordering
- Linear integration (when MCP available)
- Multiple issue templates (features, bugs, technical tasks, user stories, APIs)

**Use when:**
- Breaking down PRDs into development tasks
- Converting requirements into sprint backlog
- Creating Linear/GitHub issues from documentation
- Generating task lists with proper dependencies

---

### Issue Review
**Location:** `issue-review/`
**Description:** Reviews Linear/GitHub issues for technical accuracy, codebase alignment, and completeness before implementation.

**Features:**
- Cross-references issues against actual codebase structure
- Validates database models, APIs, and service references
- Identifies missing edge cases and error handling
- AI-driven time estimation for implementation
- Suggests issue splitting when scope is too large
- Recommends issue ordering based on dependencies
- Checks for architectural concerns and security implications

**Use when:**
- Reviewing issue specifications before implementation (especially in Claude Code)
- Validating technical accuracy against codebase
- Assessing if an issue should be split
- Estimating AI implementation time
- Checking for missing edge cases or details

---

### AI Spec
**Location:** `ai-spec/`
**Description:** Creates specifications for AI-assisted development that enable test-driven, incremental implementation.

**Features:**
- Three-tier structure: SPECIFY (what/why), PLAN (how), TASKS (build steps)
- Gherkin acceptance criteria for clear test definitions
- API contracts and data model design
- Incremental, testable task breakdown
- Templates for full and minimal specifications

**Use when:**
- Building new features, API endpoints, or services with AI coding assistants
- Planning a feature that needs clear specification
- Creating test-driven development specs
- Breaking down complex implementations into testable steps

---

### Context7 Docs
**Location:** `context7-docs/`
**Description:** Delegates documentation lookups to a specialized sub-agent to manage context window size.

**Features:**
- Spawns sub-agent to search Context7 for library documentation
- Automatic web search fallback if library not in Context7
- Returns focused summaries with code examples
- Keeps main conversation context lean
- Model selection guidance (haiku default, sonnet for complex queries)

**Use when:**
- Looking up documentation for any programming library or framework
- Finding code examples for specific functionality
- Researching API usage patterns
- Getting current documentation beyond training data cutoff

---

### Diagram
**Location:** `diagram-skill/`
**Description:** Round-trip workflow for exporting Mermaid diagrams to Excalidraw for visual editing, importing them back, and cleaning up artifacts.

**Features:**
- `/diagram export` — Convert Mermaid code blocks to `.excalidraw` files for visual editing
- `/diagram import` — Convert edited `.excalidraw` files back to Mermaid and update source markdown
- `/diagram cleanup` — Find and delete temporary `.excalidraw` workshop artifacts
- Post-processing to restore subgraphs, styles, and node IDs lost during conversion
- Round-trip fidelity tracking with known limitations documented

**Use when:**
- Visually editing Mermaid diagrams in Excalidraw
- Running diagram workshops where visual drag-and-drop editing is needed
- Converting between Mermaid and Excalidraw formats
- Cleaning up `.excalidraw` files after re-importing to Mermaid

---

### Docs Agent
**Location:** `docs-agent/`
**Description:** Automated documentation generator and query system that analyzes your codebase and maintains a comprehensive `docs/` directory.

**Features:**
- `/update-docs` — Analyze codebase and generate/update documentation (architecture, API reference, database, conventions, data flow)
- `/docs` — Query existing documentation with three modes: section retrieval, search queries, and summaries
- Incremental updates — tracks changes via `.last-update` with commit hash, only regenerates affected sections
- Mermaid diagrams for architecture and data flow visualization
- Monorepo-aware — generates per-app documentation subdirectories
- `--full`, `--section <name>`, and `--dry-run` flags for update control

**Use when:**
- Starting work on an unfamiliar codebase and need orientation
- Generating comprehensive project documentation from scratch
- Keeping docs in sync after significant code changes
- Looking up API endpoints, database schema, or architectural patterns
- Onboarding new team members to a project

---

## Skill Workflow

These skills work together to support the complete product development lifecycle:

```
┌─────────────────────┐
│ Product Manager     │  1. Conduct discovery & create PRD
│ - Interview users   │     with UI mockups and user flows
│ - Create PRD        │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Issue Creator       │  2. Break down PRD into
│ - Generate issues   │     actionable development tasks
│ - Add criteria      │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Issue Review        │  3. Validate issues against codebase
│ - Check accuracy    │     before implementation
│ - Estimate time     │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ AI Spec             │  4. Create detailed technical specs
│ - Define tests      │     for AI-assisted implementation
│ - Plan tasks        │
└─────────────────────┘
```

**Supporting skills:**
- **Context7 Docs** — Use anytime during development to look up library documentation without bloating context
- **Diagram** — Export Mermaid diagrams to Excalidraw for visual editing workshops, then import back
- **Docs Agent** — Generate and query comprehensive project documentation from your codebase

**Example workflow:**
1. Use **Product Manager** to create a PRD for "Multi-channel listing feature"
2. Use **Issue Creator** to convert the PRD into 15 development issues
3. Use **Issue Review** to validate each issue against your codebase before starting work
4. Use **AI Spec** to create detailed specifications for complex issues before implementation

---

## Installation

Skills are installed in different locations depending on your environment:

### 🌐 Claude Web (claude.ai)

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

**Issue Creator Skill:**
```
"Create issues from this PRD"
"Break down this feature into development tasks"
"Convert this document to Linear tickets"
"Generate development issues from these requirements"
```

**Issue Review Skill:**
```
"Review this issue against the codebase"
"Check if this ticket makes sense"
"Does this issue have any technical problems?"
"Validate this issue before I start implementing"
```

**AI Spec Skill:**
```
"Write a spec for the authentication feature"
"Create a specification for this API endpoint"
"Plan out this feature with test-driven tasks"
"Break down this implementation into testable steps"
```

**Context7 Docs Skill:**
```
"Look up the FastAPI authentication docs"
"How do I use React useEffect cleanup?"
"Find documentation for Prisma relations"
"What's the API for lodash debounce?"
```

**Diagram Skill:**
```
"/diagram export" or "Export this diagram to Excalidraw"
"/diagram import" or "Convert this Excalidraw back to Mermaid"
"/diagram cleanup" or "Clean up workshop files"
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

### 2026-02-13
- Added Docs Agent skill v1.0
- Automated codebase documentation generation into `docs/` directory
- `/update-docs` command with incremental updates, `--full`, `--section`, and `--dry-run` flags
- `/docs` command with section retrieval, search query, and summary modes
- Monorepo-aware with per-app documentation subdirectories
- Change tracking via `.last-update` commit hash for incremental regeneration

### 2025-01-29
- Added Diagram skill v1.0
- Mermaid to Excalidraw export via Playwright automation
- Excalidraw to Mermaid import with post-processing to restore subgraphs and styles
- Cleanup command for removing temporary `.excalidraw` workshop artifacts

### 2025-01-19
- Added Context7 Docs skill v1.0
- Delegates documentation lookups to sub-agent for context management
- Supports Context7 with web search fallback
- Added AI Spec skill v1.0
- Three-tier specification structure (SPECIFY/PLAN/TASKS)
- Test-driven development workflow for AI assistants

### 2024-10-29
- Added Issue Review skill v1.0
- Reviews issues for technical accuracy and codebase alignment
- AI-driven time estimation and scope assessment
- Updated Product Manager skill v2.1 with user flow diagram generation

### 2024-10-28
- Added Issue Creator skill v1.0
- Supports PRD-to-issue conversion with BDD acceptance criteria
- Includes Linear integration and dependency ordering

### 2024-10-24
- Added Product Manager skill v2.0
- Added UI mockup creation capability
- Updated installation instructions for all environments

---

**Note:** This is a community-maintained library. Skills are not officially supported by Anthropic. Use at your own discretion and always review skill contents before installation.
