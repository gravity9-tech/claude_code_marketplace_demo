---
description: Initialize wiki documentation from codebase with evidence-backed content, ASCII diagrams, and context skills.
argument-hint: <codebase_path> <wiki_location> [user_context]
allowed-tools: Agent, Write, Bash, Glob
model: sonnet
---

# DeepWiki Init Command

Initialize comprehensive wiki documentation from a codebase. Idempotent - does nothing if output already exists.

## Input Parameters

- **Argument 1** (`$1` - required): `codebase_path` - Root directory to analyze (e.g., `./`, `/path/to/project`)
- **Argument 2** (`$2` - required): `wiki_location` - Output directory for wiki (e.g., `./wiki`, `/path/to/docs/wiki`)
- **Argument 3** (`$3` - optional): `user_context` - Optional guidance (e.g., "small e-commerce PWA")

## Workflow

### Phase 0: Idempotency Check and CLAUDE.md Setup

**Check if wiki already exists.** If `{wiki_location}/README.md` exists:

1. Display message:
   ```
   Wiki already exists at {wiki_location}/

   To regenerate, either:
   - Delete the existing wiki: rm -rf {wiki_location}
   - Specify a different output location
   ```

2. **Stop execution.** Do not proceed to other phases.

This ensures the command is idempotent - running it multiple times with the same arguments has no effect after the first successful run.

**CLAUDE.md Setup:** Before proceeding, ensure CLAUDE.md exists:

```bash
test -f ./CLAUDE.md && echo "exists" || echo "missing"
```

If CLAUDE.md is missing, **run `/init` to create it**:

```
⚠ CLAUDE.md not found in project root.

Running /init to generate CLAUDE.md...
```

Use the Skill tool to invoke `/init`:
```
Skill: init
```

This will analyze the project and generate a proper CLAUDE.md with build commands, test instructions, and coding conventions.

**Wait for /init to complete** before proceeding.

If CLAUDE.md exists but has no `## Skills` section, append the skills section to it:

```markdown

## Skills

The following context skills are available. When a query matches a skill's purpose, invoke it using the Skill tool to load relevant documentation before responding.

| Skill | When to Use |
|-------|-------------|

<!-- Skills are automatically registered by /deepwiki:sync -->
```

### Phase 1: Check for Resume or Generate Structure

1. Check if `{wiki_location}/.temp/todo.md` exists with incomplete items

2. If exists with unchecked items:
   - Display: `Resuming from: [N] incomplete pages`
   - Skip to Phase 2

3. If not exists:
   - Call **deepwiki-planner** subagent:
     ```json
     {
       "codebase_path": "$1",
       "wiki_location": "$2",
       "user_context": "$3"
     }
     ```
   - Wait for structure file creation
   - Create `{wiki_location}/.temp/todo.md` from structure:
     ```markdown
     ---
     generated_at: [ISO timestamp]
     total_pages: [count]
     completed: 0
     ---

     # Documentation Generation Todo List

     - [ ] **overview/system-overview.md** — [purpose]
     - [ ] **overview/technology-stack.md** — [purpose]
     - [ ] **architecture/system-architecture.md** — [purpose]
     ...
     ```
   - Display todo list to user

### Phase 2: Generate Pages by Section (Parallel)

1. Group incomplete items by section (e.g., `overview/*`, `architecture/*`)

2. For each section:
   - Create `{wiki_location}/.temp/queue.md` (if not exists) to track concurrent results:
     ```markdown
     ---
     section: [section_name]
     status: processing
     agents_running: [count]
     ---

     # Pending Results

     [Result entries from subagents]
     ```

3. Launch parallel **deepwiki-doc-generator** subagents for all pages in section:
   ```json
   {
     "page_path": "[page_path]",
     "page_purpose": "[purpose]",
     "required_sections": [...],
     "required_diagrams": [...],
     "source_files": [...],
     "codebase_path": "$1",
     "wiki_location": "$2",
     "sitemap_path": "{wiki_location}/.temp/documentation_structure.md"
   }
   ```

4. Wait for all agents in section to complete

5. **Verify and persist files for each completed agent:**
   - Check if `{wiki_location}/[page_path]` file exists
   - If file exists: Mark todo item as `[x]` (completed)
   - If file NOT exists:
     * Extract generated content from agent output
     * Create directory: `mkdir -p {wiki_location}/[section]/`
     * Write file with extracted content using Write tool
     * Mark todo item as `[x]` (completed)
   - Display: `✓ [page_path]` (verified and persisted)

6. Merge verified results into todo list (atomically update `[ ]` → `[x]` for completed items)

7. Display progress: `✓ Section [name]: [N] pages generated ([Total]/[Max])`

8. Continue to next section

### Phase 3: Generate Context Skills

After all wiki pages are generated, create Claude Code skills for on-demand context loading.

1. Call **deepwiki-skill-generator** subagent:
   ```json
   {
     "wiki_path": "$2",
     "skills_output_path": "{home}/.claude/skills",
     "source_project": "{absolute_path_to_codebase}"
   }
   ```

2. The skill generator will:
   - Analyze wiki structure (top-level directories)
   - Generate `pandora-{section}` skills for each wiki section
   - Add tracking metadata to each SKILL.md frontmatter
   - Update the pandora registry at `{home}/.claude/skills/.pandora-registry.json`
   - Save skills to user's home directory (`{home}/.claude/skills/pandora-{section}/`)

3. **Register each skill in CLAUDE.md:**

   For each generated skill, add a row to the skills table in CLAUDE.md:

   ```markdown
   | pandora-{skill_name} | {skill_description}. Invoke when {trigger_condition}. |
   ```

   Insert each row before the `<!-- Skills are automatically registered` comment line.

4. Display skill generation results:
   ```
   ✓ Context skills generated:
     pandora-overview      3 pages  → registered in CLAUDE.md
     pandora-architecture  5 pages  → registered in CLAUDE.md
     ...

   Registry: {home}/.claude/skills/.pandora-registry.json
   CLAUDE.md updated with skill references.
   ```

**Why `{home}/.claude/skills/`?**
- Skills are user-specific, not project files
- Available across all Claude Code sessions
- Doesn't pollute the project repository
- Each developer generates their own from the wiki

**Pandora Skill Tracking:**
- All skills use `pandora-` prefix to distinguish from user-created skills
- Each SKILL.md contains `_generator`, `_source_project`, `_generated_at` metadata
- All skills tracked in `.pandora-registry.json` for easy management
- Use `/deepwiki:skills` to list and manage pandora-generated skills

### Phase 4: Report Completion

When all items checked:

```
✓ Documentation generation complete!
  Total pages: [N]
  Location: {wiki_location}/
  Structure: {wiki_location}/.temp/documentation_structure.md
  Todo list: {wiki_location}/.temp/todo.md

✓ Context skills generated at {home}/.claude/skills/pandora-*/
✓ Registry updated: {home}/.claude/skills/.pandora-registry.json
✓ Skills registered in ./CLAUDE.md

Tracking mechanisms:
  1. Prefix: All skills named pandora-{section}
  2. Metadata: Each SKILL.md contains _generator, _source_project, _generated_at
  3. Registry: All skills tracked in .pandora-registry.json

Claude will now auto-discover and load relevant wiki context when you:
  - Ask about architecture → loads pandora-architecture skill → reads wiki/architecture/
  - Work on features → loads pandora-features skill → reads wiki/features/
  - Ask about setup → loads pandora-development skill → reads wiki/development/

Use /deepwiki:skills to list and manage pandora-generated skills.
```

Resume tips: Run command again to continue from incomplete items.

## File Persistence Strategy

**Key Responsibility:** The slash command (orchestrator) is responsible for ensuring all generated files are persisted to disk, not the subagents.

**Persistence Flow:**

1. **Subagent generates content** → Agent completes with output containing documentation
2. **Orchestrator verifies file exists** → Check filesystem for `{wiki_location}/[page_path]`
3. **If file missing:**
   - Extract content from agent output (parse markdown between `---` markers)
   - Create parent directory: `mkdir -p {wiki_location}/[section]/`
   - Write content to file using Write tool
   - Ensure atomicity: file written before marking todo as complete
4. **Mark todo item complete** → Only after file verification succeeds

**Rationale:** Prevents silent failures where agents report completion but files don't persist. Guarantees consistency between todo list and actual filesystem state.

## Output

- **Structure File**: `{wiki_location}/.temp/documentation_structure.md` — Generated by planner
- **Todo List**: `{wiki_location}/.temp/todo.md` — Generated and managed by slash command
- **Wiki Pages**: `{wiki_location}/[page_path]/*.md` — Generated by doc generator
- **Progress**: Real-time updates shown to user after each page

## Example Usage

```
/deepwiki:init ./ ./wiki
/deepwiki:init /path/to/repo /path/to/repo/wiki "Small e-commerce PWA"
/deepwiki:init ./myproject ./myproject/docs "Microservices backend"
```

## Idempotency

This command is idempotent:
- If `{wiki_location}/README.md` exists → does nothing, suggests alternatives
- If `{wiki_location}/.temp/todo.md` exists with incomplete items → resumes from where it left off
- If neither exists → starts fresh generation

To force regeneration, delete the existing wiki first:
```bash
rm -rf ./wiki && /deepwiki:init ./ ./wiki
```
