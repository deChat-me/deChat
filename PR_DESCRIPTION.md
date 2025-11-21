# docs: Add comprehensive Claude self-push documentation

## 📚 Summary

Initial documentation for Claude's self-push capability in the deChat repository.

## 📝 Changes

This PR adds complete documentation for enabling Claude (Anthropic's AI assistant) to autonomously commit and push changes:

### New Files
- **README-claude-self-push.md** - Comprehensive documentation
  - Proxy functionality and authentication
  - Self-push workflow with retry logic
  - Strict branch naming conventions (`claude/*-<session-id>`)
  - Security guidelines and troubleshooting

- **.github/CLAUDE_WORKFLOW.md** - Practical workflow guide
  - Common development workflows (features, bugs, refactoring)
  - PR creation guidelines
  - Code review checklist for AI-generated code
  - Best practices and productivity tips

- **README.md** - Main project documentation
  - Prominent Claude integration section
  - Quick start guide
  - Project structure and contribution guidelines

- **CLAUDE_PUSH_TEST.md** - Self-push capability verification
  - Successful test execution documented
  - Timestamp: 2025-11-20 23:34:10 UTC

## ✅ Test Plan

- [x] Documentation created and committed
- [x] Branch naming convention followed (`claude/add-self-push-dechat-0152SpWuL5qzWpsjRvA6DA2k`)
- [x] Successfully pushed to remote via local proxy
- [x] All security mechanisms verified (403 on non-claude branches)

## 🔒 Security

The implementation includes:
- Branch-name validation (only `claude/*` branches allowed)
- Automatic protection of `main`/`master` branches
- Session-ID tracking for auditability
- Proxy-based authentication

## 🤖 Claude Session

- **Session-ID:** 0152SpWuL5qzWpsjRvA6DA2k
- **Branch:** claude/add-self-push-dechat-0152SpWuL5qzWpsjRvA6DA2k
- **Commit:** 96f7a01

## 📖 Documentation Preview

The documentation explains how Claude can:
1. Autonomously commit and push changes
2. Follow strict branch naming conventions
3. Implement retry logic for network failures
4. Maintain security through proxy validation

---

**Ready to merge:** ✅
**Breaking changes:** None
**Requires review:** Yes
