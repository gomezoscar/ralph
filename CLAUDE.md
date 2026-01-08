# Ralph - Autonomous AI Agent Loop

Ralph automates iterative software development by running Claude Code repeatedly until all product requirements are complete.

## How It Works

1. **PRD** (`prd.json`) - Defines user stories with acceptance criteria
2. **Loop** (`ralph.sh`) - Spawns fresh Claude Code instances
3. **Prompt** (`prompt.md`) - Instructions for each iteration
4. **Progress** (`progress.txt`) - Learnings persist across iterations

Each iteration:
- Picks highest priority story where `passes: false`
- Implements that single story
- Runs quality checks (typecheck, lint, test)
- Commits with `feat: [Story ID] - [Story Title]`
- Sets `passes: true` in prd.json
- Logs learnings to progress.txt

Loop ends when all stories pass or max iterations reached.

## Usage

```bash
# Copy to your project
cp /ralph/ralph.sh /ralph/prompt.md /path/to/project/

# Create prd.json (see prd.json.example)
cp /ralph/prd.json.example /path/to/project/prd.json

# Run (default 10 iterations)
./ralph.sh

# Or specify max iterations
./ralph.sh 20
```

## PRD Format

```json
{
  "project": "ProjectName",
  "branchName": "ralph/feature-name",
  "description": "What the feature does",
  "userStories": [
    {
      "id": "US-001",
      "title": "Short title",
      "description": "As a [user], I want [thing] so [reason]",
      "acceptanceCriteria": ["Specific requirement", "Typecheck passes"],
      "priority": 1,
      "passes": false,
      "notes": ""
    }
  ]
}
```

**Key fields:**
- `priority` - Lower = done first
- `passes` - Ralph sets to `true` when complete
- `acceptanceCriteria` - Be specific! This is how Ralph knows it's done

## Files

| File | Purpose |
|------|---------|
| `ralph.sh` | Main loop script |
| `prompt.md` | Instructions for Claude Code each iteration |
| `prd.json` | Your product requirements (create per-project) |
| `prd.json.example` | Example PRD structure |
| `progress.txt` | Auto-generated learnings log |

## Completion Signal

When all stories have `passes: true`, the agent outputs:
```
<promise>COMPLETE</promise>
```

This signals `ralph.sh` to exit successfully.

## Best Practices

1. **Small stories** - Each should complete in one context window
2. **Specific acceptance criteria** - Vague = incomplete
3. **Include quality checks** - "Typecheck passes", "Tests pass"
4. **Order by dependency** - Lower priority numbers run first
