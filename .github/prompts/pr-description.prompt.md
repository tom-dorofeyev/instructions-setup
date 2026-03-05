You are preparing a pull request description for merging branch `${input:sourceBranch}` into `${input:targetBranch}`.

Run `git diff ${input:targetBranch}...${input:sourceBranch}` to get the changes.

**Rules:**
- Describe what the code does — do not assume intentions or infer reasons.
- Focus on the context of the changes: what was added, changed, or restructured.
- No line change counts, no listing deletions — that is visible in the diff.
- Keep it concise. Summarize, don't enumerate every file.
- Group related changes under short headings where it helps clarity.

**Output:**
Write the final PR description into a new file called `pr-description.md` in the repo root.

The format should be:

```markdown
## Summary
[2–4 sentence overview of what this PR does]

## Changes
### [Heading]
[What changed and what it does]

### [Heading]
...
```
