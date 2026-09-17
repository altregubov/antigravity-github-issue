# Antigravity GitHub Issue Skill

Universal GitHub Issue Management skill for [Google Antigravity (AGY)](https://antigravity.google/) and AI pair programming assistants.

Manage, create, update, attach media to, and close GitHub issues on **any** repository directly from your agentic workflow.

---

> [!NOTE]
> **Fork & Attribution Notice**
>
> This repository is a fork and adaptation of the issue skill from the [rodydavis/agy-test](https://github.com/rodydavis/agy-test) repository (specifically [`.agents/skills/issue`](https://github.com/rodydavis/agy-test/tree/main/.agents/skills/issue)).
>
> **Key Adaptations in this Fork:**
> - **Universal Repository Support**: The upstream skill strictly enforced hardcoded operations against `rodydavis/agy-test`. This fork removes that constraint and enables seamless use with **any** GitHub repository.
> - **Dynamic Repository Detection**: Automatically resolves the target repository from git remotes (`origin`), the GitHub CLI (`gh repo view`) context, or environment variables (`GH_REPO`, `GITHUB_REPOSITORY`).
> - **Flexible CLI Overrides**: Allows explicit repository overrides using `-R <owner/repo>` or `--repo <owner/repo>` anywhere on the command line.
> - **Generalized Documentation & Templates**: Updated references and guidelines to support workflows across any personal, open-source, or enterprise codebase.

---

## Features

- 🎯 **Universal Repository Support**: Automatically detects the active repository from git remotes or CLI configuration; works in any repository out of the box.
- 📋 **Structured Issue Templates**: Standardized markdown layout with task checklists, acceptance criteria, and media placeholders.
- ☑️ **Interactive Task Checklists**: Toggle task checkboxes (`- [ ]` to `- [x]`) by 1-based index or search query directly through the helper script.
- 🖼️ **Native Media & Asset Attachments**: Attach screenshots, diagrams, and demo recordings (`.png`, `.jpg`, `.mp4`, `.mov`, `.webp`) using GitHub's native asset CDN.
- 🔗 **Commit-Linked Closure**: Verifies completion of all tasks, references the closing commit SHA, and verifies status transitions (`CLOSED` / `COMPLETED`).
- 🤖 **Antigravity Skill Integration**: Discovered automatically by Antigravity under `.agents/skills/issue/SKILL.md` or global configurations.

---

## Directory Structure

```text
.
├── README.md
└── .agents/
    └── skills/
        └── issue/
            ├── SKILL.md                               # Agent instructions & workflow
            ├── scripts/
            │   └── issue_helper.py                    # Multi-repo management helper CLI
            ├── templates/
            │   ├── issue_template.md                  # Standard issue body template
            │   └── progress_comment_template.md       # Milestone update template
            └── references/
                └── gh_issue_reference.md              # Low-level gh CLI reference
```

---

## Prerequisites

1. **GitHub CLI (`gh`)**: Ensure `gh` is installed and authenticated:
   ```bash
   gh auth status
   # If not logged in:
   gh auth login
   ```
2. **Git**: Required for automatic remote detection and commit SHA linking.
3. **Python 3**: Standard library only (no external pip dependencies required).

---

## Installation & Setup

### Option 1: Add to Current Workspace (Recommended)
Copy the `.agents/skills/issue` directory into the root of your project:
```bash
# In your target repository root:
mkdir -p .agents/skills
cp -r /path/to/antigravity-github-issue/.agents/skills/issue .agents/skills/
```
Antigravity will automatically discover the skill and make `/issue` available.

### Option 2: Global Installation for All Projects
Copy the skill to your global Antigravity configuration directory:
```bash
mkdir -p ~/.gemini/antigravity/skills
cp -r .agents/skills/issue ~/.gemini/antigravity/skills/
```

---

## Usage Guide

### Helper CLI (`issue_helper.py`)

The helper script handles repository detection, markdown checkbox rewriting, and GitHub CLI calls.

#### 1. List Open Issues
```bash
# Auto-detects current repository
python3 .agents/skills/issue/scripts/issue_helper.py list

# Or target a specific repository
python3 .agents/skills/issue/scripts/issue_helper.py -R owner/repo list
```

#### 2. View Issue Details & Tasks
```bash
python3 .agents/skills/issue/scripts/issue_helper.py view <issue-number>
```

#### 3. Create a New Issue
```bash
# Uses default structured issue template
python3 .agents/skills/issue/scripts/issue_helper.py create --title "feat: implement user authentication"

# With custom body file and labels
python3 .agents/skills/issue/scripts/issue_helper.py create \
  --title "fix: resolve memory leak" \
  --body-file issue_body.md \
  --label "bug"

# With initial media attached
python3 .agents/skills/issue/scripts/issue_helper.py create \
  --title "feat: new dashboard layout" \
  --attach "./mockup.png#Dashboard Mockup"
```

#### 4. Check Off Completed Tasks
```bash
# Toggle by task index (1-based)
python3 .agents/skills/issue/scripts/issue_helper.py check-task <issue-number> 1

# Or toggle by substring match
python3 .agents/skills/issue/scripts/issue_helper.py check-task <issue-number> "Implement core logic"
```

#### 5. Attach Images and Videos
```bash
python3 .agents/skills/issue/scripts/issue_helper.py attach <issue-number> ./screenshot.png --alt "Verification"
```

#### 6. Add Progress Comment
```bash
python3 .agents/skills/issue/scripts/issue_helper.py comment <issue-number> \
  --body "### Update: All unit tests passing."
```

#### 7. Close Issue Linked to Commit
```bash
# Automatically detects current commit SHA and closes issue
git push origin main
python3 .agents/skills/issue/scripts/issue_helper.py close <issue-number>
```

---

## Repository Resolution Order

`issue_helper.py` determines the target repository in the following order:

1. **CLI Flag**: Explicit `-R <owner/repo>` or `--repo <owner/repo>` argument.
2. **Environment Variable**: `GH_REPO` or `GITHUB_REPOSITORY`.
3. **Git Remote**: Parsed from `remote.origin.url` (`git@github.com:...` or `https://github.com/...`).
4. **GitHub CLI Context**: Resolves default repo from `gh repo view`.

---

## Direct `gh` CLI Usage

You can also use standard `gh` commands directly:

```bash
# List issues
gh issue list

# Create issue with template
gh issue create --title "feat: add feature" --body-file .agents/skills/issue/templates/issue_template.md

# Attach screenshot
gh issue edit <issue-number> --attach "./screenshot.png#Verification Screenshot"

# Close issue
gh issue close <issue-number> --reason "completed" --comment "Resolved in $(git rev-parse --short HEAD)"
```

See [gh_issue_reference.md](.agents/skills/issue/references/gh_issue_reference.md) for full syntax and JSON field options.

---

## License & Credits

- Upstream original concept and implementation by [@rodydavis](https://github.com/rodydavis) in [rodydavis/agy-test](https://github.com/rodydavis/agy-test).
- Adapted for universal repository use in this repository.