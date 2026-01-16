---
name: rlm-verify
description: Hierarchical verification cascade for validating solutions. MUST BE USED when confirming correctness of complex outputs, validating multi-step solutions, or ensuring quality before delivery. Implements three-tier verification.
allowed-tools: Read, Grep, Glob, Bash
---

# RLM Verify

Hierarchical verification cascade based on the Recursive Language Models paper (arxiv 2512.24601v1), which demonstrates 23-58% improvement through verification loops.

## When to Use

- Before delivering complex solutions
- After synthesizing multiple sub-problem results
- Validating code changes before commit
- Confirming architectural decisions
- Quality gate before PR submission
- Final check on refactoring work

## Core Framework: Three-Tier Verification

### Tier 1: Syntactic Verification
**Question**: Is it structurally correct?

Checks:
- Does it compile/parse without errors?
- Does it match the expected format/schema?
- Are all required elements present?
- Are naming conventions followed?
- Is the structure complete?

**Tools**: Linters, compilers, schema validators, format checkers

### Tier 2: Semantic Verification
**Question**: Does it mean what it should?

Checks:
- Does it solve the stated problem?
- Are edge cases handled correctly?
- Does the logic flow make sense?
- Are assumptions valid?
- Is error handling appropriate?

**Tools**: Unit tests, type checkers, code review, logic analysis

### Tier 3: Pragmatic Verification
**Question**: Does it work in the real world?

Checks:
- Does it integrate with existing code?
- Are performance requirements met?
- Is it maintainable?
- Would a human accept this solution?
- Does it follow project conventions?

**Tools**: Integration tests, performance benchmarks, human review

## Output Template

```markdown
## Verification Cascade

**Subject**: [What is being verified]
**Context**: [Why verification is needed]

### Tier 1: Syntactic Verification

| Check | Status | Details |
|-------|--------|---------|
| Compiles/Parses | [✅/❌] | [error details if failed] |
| Format Correct | [✅/❌] | [deviations if any] |
| Structure Complete | [✅/❌] | [missing elements if any] |
| Naming Conventions | [✅/❌] | [violations if any] |
| Dependencies Satisfied | [✅/❌] | [missing deps if any] |

**Tier 1 Result**: [PASS/FAIL]

### Tier 2: Semantic Verification

| Check | Status | Details |
|-------|--------|---------|
| Solves Stated Problem | [✅/❌] | [gaps if any] |
| Edge Cases Handled | [✅/❌] | [unhandled cases] |
| Logic Flow Correct | [✅/❌] | [issues if any] |
| Assumptions Valid | [✅/❌] | [invalid assumptions] |
| Error Handling | [✅/❌] | [missing handlers] |

**Tier 2 Result**: [PASS/FAIL]

### Tier 3: Pragmatic Verification

| Check | Status | Details |
|-------|--------|---------|
| Integrates Correctly | [✅/❌] | [integration issues] |
| Performance Acceptable | [✅/❌] | [metrics if measured] |
| Maintainable | [✅/❌] | [complexity concerns] |
| Follows Conventions | [✅/❌] | [deviations] |
| Human Acceptable | [✅/❌] | [UX/quality issues] |

**Tier 3 Result**: [PASS/FAIL]

### Overall Verification Result

**Status**: [VERIFIED/FAILED]
**Confidence**: [0-100]%
**Blocking Issues**: [list if any]
**Recommendations**: [suggested improvements]

### Verification Evidence

```
[Commands run, test output, or other evidence]
```
```

## Example: Verifying a Code Change

```markdown
## Verification Cascade

**Subject**: UserService OAuth2 implementation
**Context**: Post-synthesis verification before PR

### Tier 1: Syntactic Verification

| Check | Status | Details |
|-------|--------|---------|
| Compiles/Parses | ✅ | `uv run ty check` passes |
| Format Correct | ✅ | `ruff format --check` passes |
| Structure Complete | ✅ | All methods implemented |
| Naming Conventions | ✅ | Follows project style |
| Dependencies Satisfied | ✅ | oauth2-client in pyproject.toml |

**Tier 1 Result**: PASS

### Tier 2: Semantic Verification

| Check | Status | Details |
|-------|--------|---------|
| Solves Stated Problem | ✅ | OAuth2 flow works for Google |
| Edge Cases Handled | ⚠️ | Token refresh edge case needs work |
| Logic Flow Correct | ✅ | Auth → Token → Session flow verified |
| Assumptions Valid | ✅ | HTTPS assumed, documented |
| Error Handling | ✅ | All OAuth errors caught and logged |

**Tier 2 Result**: PASS (with minor issue)

### Tier 3: Pragmatic Verification

| Check | Status | Details |
|-------|--------|---------|
| Integrates Correctly | ✅ | Works with existing AuthMiddleware |
| Performance Acceptable | ✅ | <100ms auth latency |
| Maintainable | ✅ | Well-documented, follows patterns |
| Follows Conventions | ✅ | Matches existing auth code style |
| Human Acceptable | ✅ | Code reviewed by security team |

**Tier 3 Result**: PASS

### Overall Verification Result

**Status**: VERIFIED
**Confidence**: 92%
**Blocking Issues**: None
**Recommendations**:
- Add token refresh retry logic before production
- Consider caching OAuth provider metadata

### Verification Evidence

```bash
$ uv run pytest tests/auth/ -v
==================== 15 passed in 2.34s ====================

$ uv run ty check src/auth/
Success: no issues found

$ ruff check src/auth/
All checks passed!
```
```

## Verification Patterns

### Pattern 1: Fast-Fail Cascade
Stop at first failure to save time:
```
Tier 1 → [FAIL] → Stop (don't check Tier 2, 3)
Tier 1 → [PASS] → Tier 2 → [FAIL] → Stop
Tier 1 → [PASS] → Tier 2 → [PASS] → Tier 3
```

### Pattern 2: Full Audit
Check everything regardless of failures:
```
Tier 1 → [record result]
Tier 2 → [record result]
Tier 3 → [record result]
→ Comprehensive report
```

### Pattern 3: Progressive Depth
Start shallow, go deeper if needed:
```
Quick Tier 1 checks → [PASS] → Ship
Quick Tier 1 checks → [uncertain] → Deep Tier 2
Deep Tier 2 → [PASS] → Ship
Deep Tier 2 → [uncertain] → Full Tier 3
```

## Verification Commands by Language

### Python
```bash
# Tier 1: Syntactic
uv run ruff check .
uv run ruff format --check .
uv run ty check

# Tier 2: Semantic
uv run pytest tests/unit/
uv run pytest --cov=src/

# Tier 3: Pragmatic
uv run pytest tests/integration/
uv run python -m cProfile script.py
```

### TypeScript
```bash
# Tier 1: Syntactic
npx biome check .
npx tsc --noEmit

# Tier 2: Semantic
npm run test
npm run test:coverage

# Tier 3: Pragmatic
npm run test:integration
npm run build
```

## Integration with Other RLM Skills

- **After `/rlm-synthesize`**: Always verify synthesized output
- **In `/rlm-workflow`**: Verification is Phase 6 of the full pipeline
- **Failure triggers `/rlm-decompose`**: If verification fails, re-decompose with feedback
- **Before `/reflexion:memorize`**: Only memorize verified solutions

## Tips for Effective Verification

1. **Don't skip tiers** - Tier 3 failures caught early at Tier 1 save time
2. **Automate where possible** - Manual verification doesn't scale
3. **Document evidence** - Show the commands and output, not just claims
4. **Set confidence thresholds** - Define what percentage means "good enough"
5. **Make failures actionable** - Every failure should include how to fix it
6. **Verify incrementally** - Don't wait until the end to start verifying
