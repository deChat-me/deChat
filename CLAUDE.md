# CLAUDE.md - AI Assistant Guide for deChat

**Version:** 1.0.0
**Last Updated:** 2025-11-23
**Repository:** https://github.com/deChat-me/deChat

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Repository Structure](#repository-structure)
3. [Development Workflows](#development-workflows)
4. [Git Conventions](#git-conventions)
5. [Commit Message Standards](#commit-message-standards)
6. [Branch Naming Conventions](#branch-naming-conventions)
7. [Code Conventions](#code-conventions)
8. [Testing Guidelines](#testing-guidelines)
9. [Documentation Standards](#documentation-standards)
10. [AI Assistant Specific Guidelines](#ai-assistant-specific-guidelines)
11. [Common Tasks](#common-tasks)
12. [Troubleshooting](#troubleshooting)

---

## Project Overview

### What is deChat?

**deChat** is an open-source, decentralized messaging platform that prioritizes privacy and data protection. The project is built on the principles of:

- **Decentralization**: No central servers, peer-to-peer communication
- **Privacy**: End-to-end encryption by default
- **Open Source**: Fully transparent and auditable
- **Self-Hosted**: Users maintain control over their data

### Current State

The repository currently contains:

1. **Documentation**: Comprehensive guides in German and English
2. **Delta Chat Integration**: Working examples for encrypted notifications
   - One-shot notification sender (`send.py`)
   - Echo bots (simple, advanced, no-hooks variants)
   - API analysis and documentation
3. **Infrastructure**: Claude AI self-push workflow setup
4. **Whitepapers**: Technical documentation for notification systems

### Technology Stack

**Current:**
- **Language**: Python 3.9+ (for Delta Chat examples)
- **Messaging**: Delta Chat RPC Client (deltachat-rpc-client 2.4.0+)
- **Documentation**: Markdown (German primary, English secondary)

**Planned (from README):**
- Node.js 18+ for main application
- React/TypeScript for UI (inferred from structure)
- IMAP/SMTP protocols via Delta Chat

### Project Goals

1. Create a privacy-focused, decentralized chat platform
2. Provide encrypted notification systems for server administration
3. Enable self-hosted communication infrastructure
4. Maintain comprehensive documentation for developers and users

---

## Repository Structure

```
deChat/
├── .github/                          # GitHub configuration
│   └── CLAUDE_WORKFLOW.md           # Workflow guide for Claude AI
│
├── examples/                         # Working code examples
│   └── delta-chat-notifications/
│       ├── send.py                  # One-shot notification sender (production-ready)
│       ├── echobot.py              # Simple echo bot with hooks
│       ├── echobot_advanced.py     # Advanced bot features
│       ├── echobot_no_hooks.py     # Bot without event hooks
│       ├── README_SEND.md          # Usage guide for send.py
│       └── SEND_API_ANALYSIS.md    # Technical API documentation
│
├── ANLEITUNG_PROXMOX_DEBIAN.md     # German: Proxmox/Debian installation guide
├── CLAUDE_PUSH_TEST.md             # Test documentation for push workflow
├── PR_DESCRIPTION.md               # Pull request template/example
├── README-claude-self-push.md      # Claude self-push documentation
├── README.md                       # Main project README (German)
├── TASK_FOR_OTHER_SESSION.md      # Session-specific task documentation
├── WHITEPAPER_DELTA_CHAT_NOTIFICATIONS.md  # Technical whitepaper
└── CLAUDE.md                       # This file

Future Structure (from README):
src/                                 # Source code (not yet implemented)
├── components/                      # UI components
├── services/                        # Business logic
└── utils/                          # Utility functions

tests/                              # Test suites (not yet implemented)
docs/                               # Additional documentation (not yet implemented)
```

### Key Files and Their Purposes

| File | Purpose | Audience |
|------|---------|----------|
| `README.md` | Project overview, quick start guide | All users |
| `README-claude-self-push.md` | Claude self-push workflow documentation | AI assistants |
| `.github/CLAUDE_WORKFLOW.md` | Detailed workflow patterns | AI assistants, developers |
| `examples/delta-chat-notifications/send.py` | Production notification sender | System administrators |
| `WHITEPAPER_DELTA_CHAT_NOTIFICATIONS.md` | Technical architecture documentation | Architects, researchers |
| `ANLEITUNG_PROXMOX_DEBIAN.md` | Deployment guide for Debian/Proxmox | DevOps, SysAdmins |
| `CLAUDE.md` | AI assistant guide (this file) | AI assistants |

---

## Development Workflows

### Workflow 1: Feature Development

**Scenario:** Adding new functionality to the project

**Steps:**
1. **Understand Requirements**: Read user request carefully
2. **Research Context**: Examine existing code and documentation
3. **Plan Implementation**: Break down into manageable tasks
4. **Implement**: Write code following project conventions
5. **Test**: Verify functionality (manual or automated)
6. **Document**: Update relevant documentation
7. **Commit & Push**: Follow git conventions (see below)
8. **Create PR**: If requested, create pull request

**Example:**
```bash
# 1. Create feature branch
git checkout -b claude/add-user-auth-<SESSION_ID>

# 2. Implement feature
# ... write code ...

# 3. Test
npm test  # (when test framework exists)

# 4. Commit
git add .
git commit -m "feat: Add user authentication system"

# 5. Push with retry logic
git push -u origin claude/add-user-auth-<SESSION_ID>
```

### Workflow 2: Bug Fix

**Scenario:** Fixing a reported issue

**Steps:**
1. **Reproduce**: Understand the bug and how to reproduce it
2. **Identify Root Cause**: Analyze code to find the issue
3. **Implement Fix**: Make minimal changes to resolve the issue
4. **Add Regression Test**: Ensure bug doesn't reoccur
5. **Document**: Update changelog/docs if needed
6. **Commit**: Use `fix:` prefix in commit message

**Example:**
```bash
git checkout -b claude/fix-notification-delivery-<SESSION_ID>

# Fix the bug
# ... edit files ...

# Commit with reference to issue
git commit -m "$(cat <<'EOF'
fix: Resolve notification delivery race condition

- Fix race condition in notification queue
- Add mutex for concurrent access
- Update error logging for better debugging

Fixes #123
EOF
)"

git push -u origin claude/fix-notification-delivery-<SESSION_ID>
```

### Workflow 3: Documentation Updates

**Scenario:** Improving or adding documentation

**Steps:**
1. **Identify Gap**: Understand what needs documentation
2. **Research**: Gather accurate information from code/existing docs
3. **Write**: Create clear, concise documentation
4. **Review**: Check for accuracy and clarity
5. **Commit**: Use `docs:` prefix

**Language Considerations:**
- **German** is primary language for user-facing docs
- **English** for technical/international documentation
- Follow existing language patterns in each file

### Workflow 4: Refactoring

**Scenario:** Improving code quality without changing functionality

**Steps:**
1. **Analyze**: Understand current code structure
2. **Plan**: Identify improvements (reduce complexity, improve readability)
3. **Refactor**: Make changes incrementally
4. **Test**: Ensure all tests still pass
5. **Commit**: Use `refactor:` prefix

**Important:** Do not over-engineer! Keep solutions simple.

---

## Git Conventions

### Critical Rules for AI Assistants

**⚠️ MANDATORY BRANCH NAMING:**

All Claude-created branches **MUST** follow this format:
```
claude/<description>-<SESSION_ID>
```

**Components:**
- `claude/` - **REQUIRED PREFIX** (without this, push will fail with HTTP 403)
- `<description>` - Short kebab-case description of changes
- `<SESSION_ID>` - Current session identifier (provided in context)

**Examples:**
```bash
✅ claude/add-notifications-0152SpWuL5qzWpsjRvA6DA2k
✅ claude/fix-login-bug-0152SpWuL5qzWpsjRvA6DA2k
✅ claude/refactor-api-client-0152SpWuL5qzWpsjRvA6DA2k
✅ claude/docs-update-readme-0152SpWuL5qzWpsjRvA6DA2k

❌ feature/add-notifications           # Missing claude/ prefix
❌ claude/add-notifications            # Missing session ID
❌ add-notifications-0152SpWuL5qzWp   # Missing claude/ prefix
```

### Git Push Rules

**Always use:**
```bash
git push -u origin <branch-name>
```

**Retry Logic for Network Errors:**
- Retry up to 4 times with exponential backoff (2s, 4s, 8s, 16s)
- Only retry on network errors, not on 403/authorization errors

**Example with retry:**
```bash
# Attempt 1
git push -u origin claude/feature-<SESSION_ID>
# If network error, wait 2s

# Attempt 2
git push -u origin claude/feature-<SESSION_ID>
# If network error, wait 4s

# Continue up to 4 retries...
```

### Protected Operations

**Claude CAN:**
- ✅ Create new `claude/*` branches
- ✅ Push to own `claude/*` branches
- ✅ Create pull requests
- ✅ Fetch and pull from remote

**Claude CANNOT:**
- ❌ Push directly to `main` or `master`
- ❌ Delete branches (without explicit user permission)
- ❌ Force push (unless explicitly requested)
- ❌ Modify repository settings

---

## Commit Message Standards

### Format

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>: <subject>

<body>

<footer>
```

### Types

| Type | Usage | Description |
|------|-------|-------------|
| `feat` | New features | A new feature for the user |
| `fix` | Bug fixes | A bug fix for the user |
| `docs` | Documentation | Documentation only changes |
| `refactor` | Code refactoring | Code change that neither fixes a bug nor adds a feature |
| `test` | Tests | Adding or modifying tests |
| `chore` | Maintenance | Changes to build process, tools, etc. |
| `style` | Code style | Formatting, missing semi-colons, etc. (no code change) |
| `perf` | Performance | Code change that improves performance |

### Guidelines

**Subject Line:**
- Max 72 characters
- Imperative mood ("Add feature" not "Added feature")
- No period at the end
- Capitalize first letter

**Body:**
- Explain **why**, not **what** (what is in the code)
- Wrap at 72 characters
- Separate from subject with blank line
- Use bullet points for multiple changes

**Footer:**
- Reference issues: `Fixes #123`, `Closes #456`
- Breaking changes: `BREAKING CHANGE: description`

### Examples

**Good Commits:**

```bash
feat: Add Delta Chat notification support

- Implement Delta Chat API integration
- Add notification preferences for Delta Chat
- Create UI for Delta Chat account linking

This enables server administrators to receive encrypted
notifications directly on their smartphones without
relying on third-party services.
```

```bash
fix: Resolve notification delivery race condition

- Add mutex for concurrent notification queue access
- Implement proper error handling for failed sends
- Add retry logic with exponential backoff

The race condition occurred when multiple threads
tried to access the notification queue simultaneously.

Fixes #142
```

```bash
docs: Add German installation guide for Proxmox

- Create comprehensive Proxmox/Debian setup guide
- Include step-by-step installation instructions
- Add troubleshooting section for common issues
```

**Bad Commits:**

```bash
❌ update stuff
❌ fix bug
❌ changes
❌ WIP
❌ oops, forgot to add this file
```

### Commit Message Template

Use heredoc for multi-line commit messages:

```bash
git commit -m "$(cat <<'EOF'
<type>: <subject>

<body with detailed explanation>

<footer>
EOF
)"
```

---

## Branch Naming Conventions

### Standard Format

```
claude/<description>-<SESSION_ID>
```

### Description Guidelines

**Good descriptions:**
- Short and descriptive (2-5 words)
- Use kebab-case
- Action-oriented
- Specific to the change

**Examples:**
```bash
claude/add-user-authentication-abc123xyz
claude/fix-memory-leak-abc123xyz
claude/refactor-message-handler-abc123xyz
claude/docs-api-reference-abc123xyz
claude/update-dependencies-abc123xyz
```

### Finding Your Session ID

Your session ID is provided in the context. It follows the pattern:
```
<prefix>-<unique-identifier>
```

Example: `0152SpWuL5qzWpsjRvA6DA2k`

---

## Code Conventions

### General Principles

1. **Simplicity Over Complexity**
   - Avoid over-engineering
   - Write minimal code to solve the problem
   - Don't add features that weren't requested

2. **Readability**
   - Use clear, descriptive variable names
   - Add comments only where logic isn't self-evident
   - Keep functions small and focused

3. **Security**
   - No hardcoded credentials
   - Validate input at system boundaries
   - Use parameterized queries
   - Follow OWASP best practices

### Python Code Style

**Current examples follow:**
- PEP 8 style guide
- Type hints for function signatures
- Docstrings for modules and public functions
- Clear error messages with user-friendly output

**Example from `send.py`:**
```python
def send_notification(message: str, accounts_dir: str) -> None:
    """
    Send one-shot notification via Delta Chat.

    Args:
        message: Notification text to send
        accounts_dir: Path to Delta Chat accounts directory

    Raises:
        SystemExit: On configuration or connection errors
    """
    # Implementation...
```

### Documentation Style

**Inline Comments:**
- Use sparingly
- Explain **why**, not **what**
- Keep up-to-date with code changes

**Docstrings:**
- All public functions need docstrings
- Include Args, Returns, Raises sections
- Provide examples for complex functions

### File Naming

- **Python**: `snake_case.py`
- **Markdown**: `UPPERCASE.md` for docs, `lowercase.md` for guides
- **Config**: Follow ecosystem conventions

---

## Testing Guidelines

### Current State

Testing infrastructure is not yet implemented. When adding tests:

### Test Structure (Planned)

```
tests/
├── unit/           # Unit tests for individual functions
├── integration/    # Integration tests for components
└── e2e/           # End-to-end tests
```

### Testing Commands (Planned)

```bash
# Run all tests
npm test

# Run with coverage
npm run test:coverage

# Run in watch mode
npm run test:watch
```

### Test Writing Guidelines

1. **Arrange-Act-Assert**: Structure tests clearly
2. **One assertion per test**: Test one thing at a time
3. **Descriptive names**: Test name should describe what is tested
4. **Mock external dependencies**: Don't rely on network/filesystem
5. **Test edge cases**: Not just happy path

---

## Documentation Standards

### File Organization

**Location:**
- User guides: Root directory
- Technical docs: Root or `docs/` (when created)
- API docs: Near relevant code
- Examples: `examples/` directory

### Language Guidelines

**German (Primary):**
- User-facing documentation
- Installation guides
- Quick start guides
- README files

**English:**
- Technical whitepapers
- API documentation
- Code comments
- International audience docs

### Documentation Checklist

When adding new features, update:
- [ ] Main README.md (if user-facing)
- [ ] Relevant guides (installation, usage, etc.)
- [ ] Code comments and docstrings
- [ ] API documentation (if applicable)
- [ ] CHANGELOG (when exists)
- [ ] Examples (if helpful)

### Markdown Style

- Use ATX-style headers (`# Header`)
- Include table of contents for long docs
- Use code fences with language tags
- Add emoji sparingly (only when already established in the project)
- Use tables for structured data
- Include examples for complex concepts

---

## AI Assistant Specific Guidelines

### Best Practices for Claude

1. **Always Read Before Modifying**
   - Never propose changes to code you haven't read
   - Use Read tool to understand existing code
   - Check related files for context

2. **Use Specialized Tools**
   - Use Glob/Grep for searching, not bash commands
   - Use Edit tool for modifications, not sed/awk
   - Use Read tool for viewing files, not cat

3. **Context Awareness**
   - Check existing patterns in the codebase
   - Follow established conventions
   - Maintain consistency with existing code

4. **Communication**
   - Output text directly to communicate with user
   - Never use bash echo or comments to talk to user
   - Be clear about what you're doing and why

5. **Error Handling**
   - Always check for errors
   - Provide clear error messages
   - Suggest solutions when things fail

### Working with this Repository

**Before Starting:**
1. Read relevant documentation files
2. Check existing examples in `examples/`
3. Understand the current state of the project
4. Verify session ID for branch naming

**During Development:**
1. Follow commit message conventions
2. Use correct branch naming format
3. Test changes before committing
4. Update documentation as needed

**After Completion:**
1. Verify all changes are committed
2. Push with proper branch name and retry logic
3. Provide summary of changes to user
4. Suggest next steps if applicable

### Common Pitfalls to Avoid

❌ **Don't:**
- Create files unnecessarily (prefer editing existing ones)
- Over-engineer solutions
- Add features that weren't requested
- Use wrong branch naming format
- Push without proper session ID
- Add excessive comments or type annotations to unchanged code
- Create abstractions for one-time operations

✅ **Do:**
- Make minimal, focused changes
- Follow existing patterns
- Keep solutions simple
- Verify branch name before pushing
- Test changes when possible
- Update relevant documentation
- Ask for clarification when requirements are unclear

---

## Common Tasks

### Task 1: Add a New Feature

```bash
# 1. Research existing code
# Use Read, Glob, Grep tools to understand codebase

# 2. Create feature branch
git checkout -b claude/add-feature-name-<SESSION_ID>

# 3. Implement feature
# Use Edit/Write tools

# 4. Test functionality
# Manual testing or automated tests

# 5. Update documentation
# Edit relevant .md files

# 6. Commit changes
git add .
git commit -m "feat: Add feature name with brief description"

# 7. Push with retry logic
git push -u origin claude/add-feature-name-<SESSION_ID>
```

### Task 2: Fix a Bug

```bash
# 1. Reproduce the bug
# Understand the issue

# 2. Locate the bug
# Use Grep to find relevant code

# 3. Create fix branch
git checkout -b claude/fix-bug-description-<SESSION_ID>

# 4. Implement fix
# Make minimal changes

# 5. Verify fix
# Test that bug is resolved

# 6. Commit
git commit -m "fix: Resolve bug in component X

- Detailed explanation of fix
- Why bug occurred
- How fix resolves it

Fixes #issue-number"

# 7. Push
git push -u origin claude/fix-bug-description-<SESSION_ID>
```

### Task 3: Update Documentation

```bash
# 1. Identify documentation gap
# 2. Research accurate information
# 3. Create/update documentation

# For new file:
# Use Write tool with proper content

# For existing file:
# Use Read to see current content
# Use Edit to make changes

# 4. Commit
git add <documentation-files>
git commit -m "docs: Update/Add documentation for X"

# 5. Push
git push -u origin claude/docs-update-<SESSION_ID>
```

### Task 4: Refactor Code

```bash
# 1. Understand current code
# Read and analyze existing implementation

# 2. Plan refactoring
# Identify improvements without changing behavior

# 3. Create refactor branch
git checkout -b claude/refactor-component-<SESSION_ID>

# 4. Refactor incrementally
# Make small, testable changes

# 5. Verify tests still pass
# Ensure no behavior changes

# 6. Commit
git commit -m "refactor: Simplify component X logic"

# 7. Push
git push -u origin claude/refactor-component-<SESSION_ID>
```

---

## Troubleshooting

### Issue: HTTP 403 on Push

**Symptom:** Push rejected with "403 Forbidden"

**Cause:** Branch name doesn't follow required format

**Solution:**
```bash
# Check current branch
git branch --show-current

# If incorrect, create correct branch
git checkout -b claude/correct-name-<SESSION_ID>

# Move changes to correct branch
git cherry-pick <commit-hash>
```

### Issue: HTTP 502 Bad Gateway

**Symptom:** Proxy responds with 502 error

**Cause:** Repository not authorized in proxy configuration

**Solution:**
- Verify you're working on correct repository
- Check proxy configuration
- Contact administrator if issue persists

### Issue: Merge Conflicts

**Symptom:** Push fails due to conflicts

**Cause:** Remote branch has been updated

**Solution:**
```bash
# Fetch latest changes
git fetch origin

# Merge remote branch
git merge origin/claude/your-branch-<SESSION_ID>

# Resolve conflicts
# Use Edit tool to fix conflicts

# Complete merge
git add .
git commit -m "merge: Resolve conflicts with remote"

# Push again
git push -u origin claude/your-branch-<SESSION_ID>
```

### Issue: Can't Find Session ID

**Symptom:** Don't know what session ID to use

**Solution:**
- Session ID is provided in your context
- Check the initial instructions from user
- Look for format like: `0152SpWuL5qzWpsjRvA6DA2k`
- Or check current branch: `git branch --show-current`

### Issue: Python Import Errors

**Symptom:** `ModuleNotFoundError` when running examples

**Solution:**
```bash
# Install dependencies
pip install deltachat-rpc-server deltachat-rpc-client

# Or use virtual environment
python3 -m venv venv
source venv/bin/activate
pip install deltachat-rpc-server deltachat-rpc-client
```

### Issue: No Tests Available

**Symptom:** Cannot run tests

**Cause:** Test infrastructure not yet implemented

**Solution:**
- Perform manual testing
- Document test steps
- Note that tests will be added in future

---

## Quick Reference

### Essential Commands

```bash
# Check current branch
git branch --show-current

# Create correct branch
git checkout -b claude/<description>-<SESSION_ID>

# Stage changes
git add .

# Commit with message
git commit -m "type: subject"

# Push with retry logic
git push -u origin claude/<description>-<SESSION_ID>

# Check status
git status

# See recent commits
git log --oneline -5
```

### File Locations

| Need | Location |
|------|----------|
| Project overview | `README.md` |
| Claude workflow | `.github/CLAUDE_WORKFLOW.md` |
| Push documentation | `README-claude-self-push.md` |
| Python examples | `examples/delta-chat-notifications/` |
| API docs | `examples/delta-chat-notifications/SEND_API_ANALYSIS.md` |
| Deployment guide | `ANLEITUNG_PROXMOX_DEBIAN.md` |
| Technical whitepaper | `WHITEPAPER_DELTA_CHAT_NOTIFICATIONS.md` |

### Important Links

- **Repository:** https://github.com/deChat-me/deChat
- **Issues:** https://github.com/deChat-me/deChat/issues
- **Delta Chat:** https://delta.chat
- **Conventional Commits:** https://www.conventionalcommits.org/

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2025-11-23 | Initial CLAUDE.md creation |

---

## Contributing to This Guide

This guide should be updated when:
- New conventions are established
- Repository structure changes
- New workflows are introduced
- Common issues are discovered
- Technology stack evolves

**To update:** Create a branch following conventions and submit changes with clear commit messages.

---

## Questions or Issues?

1. Check this documentation first
2. Review `.github/CLAUDE_WORKFLOW.md`
3. Consult `README-claude-self-push.md`
4. Ask the user for clarification
5. Create an issue if problem persists

---

**Remember:** This is a living document. Keep it updated as the project evolves!

**Last Updated by:** Claude Code
**Contact:** Via GitHub Issues
