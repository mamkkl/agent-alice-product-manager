# TOOLS.md - Local Notes

Skills define _how_ tools work. This file is for _your_ specifics — the stuff that's unique to your setup.

## What Goes Here

Things like:

- Camera names and locations
- SSH hosts and aliases
- Preferred voices for TTS
- Speaker/room names
- Device nicknames
- Anything environment-specific

## Examples

```markdown
### Cameras

- living-room → Main area, 180° wide angle
- front-door → Entrance, motion-triggered

### SSH

- home-server → 192.168.1.100, user: admin

### TTS

- Preferred voice: "Nova" (warm, slightly British)
- Default speaker: Kitchen HomePod
```

## Why Separate?

Skills are shared. Your setup is yours. Keeping them apart means you can update skills without losing your notes, and share skills without leaking your infrastructure.

---

Add whatever helps you do your job. This is your cheat sheet.

## Product Management Workflow Notes

### Tool/Skill Failure Protocol (CRITICAL)
**When any tool or skill fails, follow this exact sequence:**

1. **Identify the issue**: Clearly state what failed and why
2. **Communicate immediately**: Report to Boss with clear facts - NO technical fixes, NO alternative approaches
3. **Wait for guidance**: Get Boss's direction on how to proceed
4. **Execute approved approach**: Only after receiving explicit approval
5. **Report results**: Share outcomes of approved approach

**DO NOT:**
- Attempt technical troubleshooting without approval
- Try alternative approaches without approval
- Make assumptions about solutions
- "Try and see" approaches
- Spend time on unapproved fixes

**Correct Pattern:**
> "Boss, the OpenProject skill failed due to missing dependencies. How should I proceed?"

**Incorrect Pattern:**
> "Let me check if Python is installed..." → "Let me try installing playwright..." → "Let me try a different approach..."

### Skill Usage Protocol
1. **Check available skills first**: Always list skills before attempting domain-specific operations
2. **Read SKILL.md**: Understand requirements, setup, and proper usage
3. **Follow skill patterns**: Use established workflows instead of reinventing solutions
4. **Report skill issues**: Document failures and request environment setup if needed

### Professional Boundaries
- **Never analyze source code**: Development team responsibility
- **Always plan first**: Communicate direction before execution
- **Use proper channels**: Team consultation for implementation status
- **Focus on outcomes**: User needs and business value over technical details

### Gap Analysis Approach
1. Specifications review → Team consultation → Project tools → User perspective
2. Document findings with clear recommendations
3. Present structured analysis without technical implementation details

---

## OpenProject Mention Format (CRITICAL)

**When notifying users in OpenProject, ALWAYS use proper mention tag format.**

**Format**:
```html
<mention class="mention" data-id="5" data-type="user">@The Boss</mention>&nbsp;
```

**Examples**:
- Boss: `data-id="5"` - `<mention class="mention" data-id="5" data-type="user">@The Boss</mention>`
- Alice: `data-id="6"` - `<mention class="mention" data-id="6" data-type="user">@Alice Product Manager</>`
- Metric: `data-id="7"` - `<mention class="mention" data-id="7" data-type="user">@Metric Business Analyst</>`

**Pending IDs** (waiting for Boss/skill update):
- Product Designer: `data-id="?"`
- UI/UX Designer: `data-id="?"`
- Fullstack Developer: `data-id="?"`
- QA Engineer: `data-id="?"`

**Note**: Boss will update OpenProject skill to include project member listing functionality. IDs will be added when skill is updated.

**Usage in Comments**:
```bash
# Create temp file with mention
cat > /tmp/comment.txt << 'EOF'
<mention class="mention" data-id="5" data-type="user">@The Boss</mention>

**Decision Needed: Topic**

Context: ... 
Options: ...
Recommendation: ...
EOF

# Add comment
python3 openproject_cli.py add-comment --id 37 --comment "$(cat /tmp/comment.txt)"
```

**Critical Note**: Plain text `@The Boss` does NOT trigger notifications. Must use `<mention>` tag format.

**Note from 2026-03-12 incident**: Boss confirmed plain text mentions don't trigger notification system. Must use proper `<mention>` tag with `data-id`, `data-type`, and correct `data-text`.

---

## OpenProject Multi-Line Comment Pattern (CRITICAL)

**When adding multi-line comments to OpenProject work packages, ALWAYS use file-based approach.**

**Pattern**:
1. Write comment content to temp file
2. Verify formatting with `cat /tmp/file.txt`
3. Use command substitution: `--comment "$(cat /tmp/file.txt)"`
4. Verify result with `list-comments`

**Example**:
```bash
# Create temp file with multi-line content
cat > /tmp/escalation.txt << 'EOF'
@The Boss

**Decision Needed: Trial Cancellation Policy**

**Context**: Metric asks if users can opt out of trial early. This affects our ability to measure 90% activation KPI.

**Options**:
- Option A: No early cancellation - users complete full 30 days
- Option B: Allow cancellation anytime - immediate transition to limited tier

**Vision/Strategy Impact**:
- Option A: Ensures users experience full trial value → better Pro conversion measurement
- Option B: User autonomy respected → higher satisfaction, but reduces opportunity to demonstrate value

**Recommendation**: Option A (no early cancellation)
**Rationale**: Aligns with Phase 1A validation - users must experience full 30-day value.

Please confirm so Metric can complete specifications.
EOF

# Verify formatting
cat /tmp/escalation.txt

# Add comment with substitution
python3 skills/openclaw-skill-openproject/home/node/.claude/skills/openproject/home/node/.claude/skills/openproject/home/node/.claude/skills/openproject/scripts/openproject_cli.py \
  add-comment --id 37 --comment "$(cat /tmp/escalation.txt)"

# Verify result
python3 skills/openclaw-skill-openproject/home/node/.claude/skills/openproject/home/node/.claude/skills/openproject/home/node/.claude/skills/openproject/scripts/openproject_cli.py \
  list-comments --id 37 --limit 3
```

**Common Mistakes**:
- ❌ Using `\n` in command line (appears literally as escape sequence)
- ❌ Not verifying formatting after adding
- ❌ Using quotes incorrectly with newlines
- ❌ Passing multi-line strings directly on command line

**Verification Steps (MANDATORY)**:
1. After creating temp file: `cat /tmp/file.txt` to verify formatting
2. After adding comment: `list-comments --id <wp_id> --limit 3` to check formatting
3. If escape sequences appear: Delete comment and re-add with file-based approach

**Note from 2026-03-12 incident**: Bash command arguments don't interpret escape sequences like `\n`. Always use file-based approach with command substitution for multi-line content.

### OpenProject Skill Command Specificity

**Always use `python3` for OpenProject skill scripts:**
- The OpenProject skill scripts require Python 3 interpreter
- Use `python3 scripts/openproject_wiki_cli.py` (not `python`)
- Use `python3 scripts/openproject_cli.py` (not `python`)
- Environment may not have `python` symlinked to Python 3

**Example**:
```bash
# Correct
python3 scripts/openproject_wiki_cli.py read-wiki --project kaironinv-dot-ai --title "Product Vision"

# Incorrect (may fail with "python: not found")
python scripts/openproject_wiki_cli.py read-wiki --project kaironinv-dot-ai --title "Product Vision"
```

**Note**: This is environment-specific but consistent across OpenClaw deployments.

### OpenProject Skill Environment Requirements

**Critical dependencies for OpenProject skill operation:**

1. **Python 3 interpreter** (`python3` command)
2. **Chromium browser** (for Playwright automation)
   - Installation: `playwright install chromium`
   - Required for wiki operations (read-wiki, write-wiki)
3. **Environment variables**:
   - `OPENPROJECT_BASE_URL`: Base URL of OpenProject instance (e.g., `https://pm.kaironinv.ai`)
   - `OPENPROJECT_TOKEN`: API token for authentication (if using API methods)
   - `OPENPROJECT_USERNAME` / `OPENPROJECT_PASSWORD`: Credentials for browser automation
4. **Playwright Python package**: `playwright` (installed via `pip install playwright`)

**Common failure modes**:
- "Chromium not installed. Run: playwright install chromium" → Install Chromium
- "Environment variable OPENPROJECT_BASE_URL is required but not set" → Set variable
- "python: not found" → Use `python3` command instead

**Note**: These dependencies are typically configured by Boss during environment setup. If missing, communicate failure to Boss (do not attempt installation/configuration yourself).

---

## Repository Roles and Boundaries

### Repository Purpose & Scope

**mamkkl/alice-aipm-core-context**
- **Purpose:** Core context files only - identity, protocols, memory
- **Scope:** AGENTS.md, TOOLS.md, WORKFLOW.md, SOUL.md, USER.md, MEMORY.md, HEARTBEAT.md, IDENTITY.md, OWNED_SKILLS.md, ROLES_AND_RESPONSIBILITIES.md
- **NOT for:** Workspace files, skills, memory/, runbooks/, scripts/, tests/, project submodules
- **NOT for:** Guidelines that are shared via runbooks (see runbooks/guidelines/)
- **Branch:** main (not master)
- **Update frequency:** When core protocols/identity change

**mamkkl/openclaw-workspace-initial-runbooks**
- **Purpose:** Standard operational guides and best practices
- **Scope:** Runbooks, guidelines, templates, README
- **Use:** Reference for setup, integration patterns, community contributions
- **Branch:** main
- **Update frequency:** When runbooks are updated in source repository

### Workspace vs Repository

**Workspace:**
- **Path:** Full working directory (`/home/node/.openclaw/workspace/`)
- **Status:** Not version-controlled (workspace is not a git repository)
- **Local files:** Memory/, skills/, runbooks/, scripts/, etc. stay in workspace only
- **Version control:** Only core context files tracked in alice-aipm-core-context repo

**Core Context Repository:**
- **Rule:** Only core context files go into alice-aipm-core-context repo
- **Purpose:** Identity, protocols, and operational procedures
- **Boundary:** Do not include workspace-specific files, skills, or project work

---

## Git Branching Convention

**Default branch:** main (not master)

**Action required:**
- Use main branch for all repositories
- Do not assume master branch exists
- If creating new repositories, default to main branch
- Rename any existing master branches to main

**Rationale:** Main is becoming standard over master due to inclusive language conventions.

**Repository status:**
- alice-aipm-core-context: main ✓ (master deleted)
- openclaw-workspace-initial-runbooks: (verify branch name)

---

## Environment Constraints Awareness

### Containerized/Runtime Constraints
Your workspace may run in a constrained environment. Common limitations:

- **User**: Runs as non‑root user (`node`, `openclaw`, etc.).
- **sudo**: Not available – cannot use `sudo` for privilege escalation.
- **Elevated exec**: `elevated: true` may not be available in your runtime configuration.
- **File ownership**: Some files (`.env`, skill directories) may be owned by `root`; permission changes require manual host intervention.
- **Network**: Full internet access, but local network services may be limited.
- **Shared `/tmp` folder**: The `/tmp` directory is SHARED across all agents in the container. Do NOT use `/tmp` for temporary files or repositories (security risk, collision risk, data leakage risk).
- **Shared environment variables**: Environment variables are SHARED across all agents in the container. Do NOT use `${VARIABLE}` expansion in command strings for tokens, keys, or credentials (security risk, scope violation risk, accidental exposure). Always embed explicit values directly in command strings.

### Implications
- Credential updates requiring file writes may fail with "EACCES: permission denied".
- **Workaround**: Ask host to adjust file permissions (e.g., `chown node:node <file>`).
- Avoid attempting `sudo` or elevated exec; rely on user‑provided permission fixes.
- **Never use `/tmp`** for temporary workspaces, git clones, or file operations. Use `.tmp/` directory within your workspace instead.
- **Never expand environment variables** for tokens, keys, or credentials in command strings. Use explicit values directly (e.g., `https://user:github_pat_XXX@github.com/repo.git` not `https://${GITHUB_TOKEN}@github.com/repo.git`). Read values from `.env` files if needed and embed in command strings.

### Workspace-Specific Temporary Directory
- **Use**: `/home/node/.openclaw/workspace/.tmp/` for all temporary file operations, git clones, and scratch work
- **Create**: Automatically with `mkdir -p /home/node/.openclaw/workspace/.tmp/` if needed
- **Cleanup**: Periodically clean up `.tmp/` contents (ask user first for large cleanups)
- **Security**: Only you have access to `.tmp/` (within your workspace boundaries)
- **Purpose**: Prevent collisions with other agents, ensure data isolation, maintain security boundaries

---

### Graphiti Memory

Persistent knowledge graph for storing and retrieving facts across sessions.

- **Connection**: Scripts connect directly to Neo4j and LiteLLM on the Docker network (no MCP intermediary)
- **Skill**: `graphiti-memory` (scripts at `~/.openclaw/skills/graphiti-memory/scripts/`)
- **Group ID**: `product-manager` (agent-specific isolation - always pass `--group-id product-manager`)
- **Installed**: Globally via clawhub (2026-03-25 04:11 UTC)

**Operations**:

**Store an Episode**:
```bash
python3 ~/.openclaw/skills/graphiti-memory/scripts/store.py --group-id product-manager "User prefers dark mode and vim keybindings"
```

**Search for Facts**:
```bash
python3 ~/.openclaw/skills/graphiti-memory/scripts/search.py --group-id product-manager "user preferences"
```

**Ingest a Memory File** (bulk import):
```bash
python3 ~/.openclaw/skills/graphiti-memory/scripts/ingest.py --group-id product-manager /path/to/memory.md
```

**Environment Variables**:
- `NEO4J_BOLT_DOMAIN`: bolt.memory.kaironinv.ai
- `NEO4J_URI`: bolt://neo4j:7687
- `NEO4J_USER`: neo4j
- `NEO4J_PASSWORD`: neo4j-2026-secure
- `LITELLM_MASTER_KEY`: sk-litellm-openclaw-2026

**Session Startup Protocol** (Graphiti-First):
```bash
# Query 1: Identity & Philosophy
python3 ~/.openclaw/skills/graphiti-memory/scripts/search.py --group-id product-manager "identity role philosophy product manager Alice integrity principles Boss partnership"

# Query 2: Protocols, Learnings, & Failures
python3 ~/.openclaw/skills/graphiti-memory/scripts/search.py --group-id product-manager "tool failure protocol skill error escalation correction learning pattern"

# Read recent memory
cat /home/node/.openclaw/workspace-product-manager/memory/$(date +%Y-%m-%d).md
```

**Performance**: 35-60 seconds (50-80% faster than file reading)

**Note**: See WORKFLOW.md for complete Graphiti-First Session Startup documentation.

---

## Related Core Context Files

**Role & Responsibilities**:
- **ROLES_AND_RESPONSIBILITIES.md**: Complete role definition for kaironinv project, including:
  - Detailed responsibilities in kaironinv project
  - Decision authority framework
  - Communication protocols
  - OpenProject integration specifics

**Operating Procedures**:
- **WORKFLOW.md**: Procedures, decision frameworks, quality assurance
- **AGENTS.md**: Skills-first protocol, session startup, self-improvement

**Identity & Philosophy**:
- **SOUL.md**: Core truths, integrity criteria, dual communication style
- **IDENTITY.md**: Personal profile (name, title, creature, vibe)
- **USER.md**: About Boss (expectations, timezone, context)

**Memory**:
- **MEMORY.md**: Long-term identity, learnings, patterns (main session only)

**Quick Reference**:
- "What tools do I have?" → Check skills list / AGENTS.md (Tool Selection Protocol)
- "How do I use the OpenProject skill?" → See runbooks/OPENPROJECT_RUNBOOK.md
- "What's my credential storage rule?" → This file (workspace organization)

