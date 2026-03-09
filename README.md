# Read Me



## Using the Grading Workflow in Other Repos

The [`classroom.yml`](.github/workflows/classroom.yml) grading workflow is published as a **reusable GitHub Actions workflow**.  
Instead of copying and maintaining the full workflow in every submission repo, you can call it from a single thin workflow file:

```yaml
# .github/workflows/classroom.yml  (in any submission repo)
name: Grade Submission
on:
  workflow_dispatch:
  issues:
    types: [opened]
  issue_comment:
    types: [created]

permissions:
  issues: write
  contents: read

jobs:
  grade:
    uses: DataScience4Psych/actions/.github/workflows/classroom.yml@main
    secrets: inherit
```

Any future improvements made to the grading logic in this template repo are immediately available to every repo that references it — no copy-paste required.

> **Secret:** If student repos are private, add a repo secret named `GRADING_PAT` containing a PAT with `repo` read access.  
> `secrets: inherit` will forward it automatically to the reusable workflow.
