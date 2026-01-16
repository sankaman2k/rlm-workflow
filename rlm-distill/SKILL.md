---
name: rlm-distill
description: Context distillation for extracting relevant information from large inputs. MUST BE USED when facing information overload, processing large codebases, or filtering context to essential elements. Uses pattern-based filtering without full processing.
allowed-tools: Read, Grep, Glob
---

# RLM Distill

Context distillation based on the Recursive Language Models paper (arxiv 2512.24601v1), which demonstrates 91% accuracy at 66% cost through intelligent filtering.

## When to Use

- Large codebase exploration (too many files to read fully)
- Information overload situations
- Extracting relevant context for sub-problems
- Reducing token usage on repetitive content
- Finding needles in haystacks
- Preparing focused context for downstream tasks

## Core Framework

### Step 1: Query Formulation

Define precisely what information is needed:
- **Specific**: "Find all authentication middleware functions"
- **Not vague**: "Find security stuff"

Good queries have:
- Clear target (what to find)
- Defined scope (where to look)
- Success criteria (how to know when done)

### Step 2: Pattern Generation

Create regex/glob patterns for filtering:

| Pattern Type | Example | Use Case |
|--------------|---------|----------|
| Filename glob | `**/*auth*.py` | Find files by name |
| Content regex | `def authenticate\(` | Find function definitions |
| Comment pattern | `# TODO:.*security` | Find specific TODOs |
| Import pattern | `from auth import` | Find usages |
| Structure pattern | `class.*Middleware` | Find class definitions |

### Step 3: Progressive Refinement

Start broad, narrow iteratively:

```
Round 1: `**/*.py` → 500 files
Round 2: `**/auth/**/*.py` → 25 files
Round 3: grep `class.*Auth` → 8 files
Round 4: Read relevant sections → 3 files, 200 lines
```

### Step 4: Relevance Scoring

Rate each extracted piece:

| Score | Meaning | Action |
|-------|---------|--------|
| 3 | Directly relevant | Include in distilled output |
| 2 | Potentially relevant | Include if space allows |
| 1 | Context only | Summarize, don't include full |
| 0 | Not relevant | Exclude |

### Step 5: Distilled Output

Produce minimal context that answers the query:
- Target: 10-20% of original size
- Preserve: Critical code, key decisions, essential context
- Remove: Boilerplate, repetition, tangential details

## Output Template

```markdown
## Context Distillation Report

**Information Need**: [What we're looking for]
**Source Scope**: [Files/tokens/lines in original]
**Target Compression**: [Percentage of original to keep]

### Filtering Strategy

| Round | Pattern | Rationale | Results |
|-------|---------|-----------|---------|
| 1 | `[pattern]` | [why this filter] | [X files/matches] |
| 2 | `[pattern]` | [narrowing reason] | [Y files/matches] |
| 3 | `[pattern]` | [final focus] | [Z files/matches] |

### Relevance Assessment

| Item | Score | Justification |
|------|-------|---------------|
| [file/section] | [0-3] | [why this score] |

### Distilled Context

```[language]
[Minimal relevant code/content - only score 3 items]
```

### Summary of Excluded Content

- [What was excluded and why]
- [Patterns observed but not included]

### Coverage Analysis

**Captured**: [What's definitely included]
**Potentially Missed**: [Acknowledged gaps]
**Confidence**: [0-100]% that query is answered

### Compression Achieved

- Original: [X tokens/lines]
- Distilled: [Y tokens/lines]
- Compression ratio: [Z]%
```

## Example: Distilling Auth Logic from Large Codebase

```markdown
## Context Distillation Report

**Information Need**: How does the authentication middleware validate JWT tokens?
**Source Scope**: 2,847 Python files, ~500k lines
**Target Compression**: 10% or less

### Filtering Strategy

| Round | Pattern | Rationale | Results |
|-------|---------|-----------|---------|
| 1 | `**/*.py` | All Python files | 2,847 files |
| 2 | `**/auth/**/*.py` + `**/middleware/**/*.py` | Auth-related dirs | 34 files |
| 3 | grep `jwt\|token\|verify` | JWT-specific code | 12 files |
| 4 | grep `class.*Middleware\|def verify` | Entry points | 4 files |

### Relevance Assessment

| Item | Score | Justification |
|------|-------|---------------|
| `auth/middleware/jwt.py` | 3 | Main JWT verification logic |
| `auth/tokens.py` | 3 | Token creation/validation |
| `auth/exceptions.py` | 2 | Error handling context |
| `auth/config.py` | 1 | Config only, not logic |

### Distilled Context

```python
# auth/middleware/jwt.py (lines 45-89)
class JWTAuthMiddleware:
    def __init__(self, secret_key: str, algorithms: list[str] = ["HS256"]):
        self.secret_key = secret_key
        self.algorithms = algorithms

    async def verify_token(self, token: str) -> dict:
        try:
            payload = jwt.decode(
                token,
                self.secret_key,
                algorithms=self.algorithms
            )
            if payload.get("exp", 0) < time.time():
                raise TokenExpiredError()
            return payload
        except jwt.InvalidTokenError as e:
            raise InvalidTokenError(str(e))

# auth/tokens.py (lines 12-28)
def create_access_token(user_id: str, expires_delta: timedelta) -> str:
    payload = {
        "sub": user_id,
        "exp": datetime.utcnow() + expires_delta,
        "iat": datetime.utcnow(),
    }
    return jwt.encode(payload, settings.SECRET_KEY, algorithm="HS256")
```

### Summary of Excluded Content

- `auth/config.py`: Just loads env vars, no logic
- `auth/exceptions.py`: Standard exception classes
- `auth/decorators.py`: Convenience wrappers, use middleware internally
- 28 other files: Unrelated to JWT verification

### Coverage Analysis

**Captured**:
- JWT decode logic
- Token expiration checking
- Algorithm configuration
- Token creation for reference

**Potentially Missed**:
- Refresh token flow (separate system)
- Key rotation mechanism (if exists)

**Confidence**: 95% that query is answered

### Compression Achieved

- Original: ~500,000 lines
- Distilled: 31 lines
- Compression ratio: 99.99%
```

## Distillation Patterns

### Pattern 1: Funnel Filter
Start with everything, progressively narrow:
```
All files → Relevant dirs → Matching content → Key sections
```

### Pattern 2: Anchor Expansion
Start from known point, expand outward:
```
Known file → Imports → Callers → Related tests
```

### Pattern 3: Cross-Reference
Find from multiple angles, intersect:
```
Files matching "auth" ∩ Files matching "jwt" ∩ Files modified recently
```

## Common Grep Patterns

```bash
# Function definitions
grep -E "def [a-z_]+\("

# Class definitions
grep -E "class [A-Z][a-zA-Z]+:"

# Imports from specific module
grep -E "from auth import|import auth"

# TODO/FIXME comments
grep -E "# (TODO|FIXME|HACK):"

# Error handling
grep -E "except.*Error|raise.*Error"

# API endpoints
grep -E "@(app|router)\.(get|post|put|delete)"
```

## Integration with Other RLM Skills

- **Before `/rlm-decompose`**: Distill context to understand problem scope
- **During `/rlm-workflow`**: Distill is Phase 2 of the full pipeline
- **For each sub-problem**: Distill relevant context before solving
- **With `/rlm-verify`**: Distill relevant tests for verification

## Tips for Effective Distillation

1. **Define success upfront** - Know when you have enough
2. **Use multiple patterns** - Different angles catch different things
3. **Score ruthlessly** - If it's not directly relevant, it's probably not needed
4. **Document exclusions** - Know what you chose not to include
5. **Acknowledge gaps** - Better to say "might have missed X" than to assume completeness
6. **Iterate quickly** - Broad → narrow → narrower → done
