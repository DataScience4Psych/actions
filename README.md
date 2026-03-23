# Read Me



## Auto-Comment Workflow

The [`auto-comment.yml`](.github/workflows/auto-comment.yml) workflow posts a redirect comment on newly-opened issues, telling students to transfer the issue to the correct lab submission repo.  
Lab repos follow the naming convention `YEAR-lab_NN` (e.g. `DataScience4Psych/2026-lab_10`).

### Quickstart

Add a thin caller workflow to your lab repo (e.g. `.github/workflows/auto-comment.yml`):

```yaml
name: Auto Comment
on:
  issues:
    types: [opened]

jobs:
  comment:
    uses: DataScience4Psych/actions/.github/workflows/auto-comment.yml@main
    with:
      lab_number: '10'   # replace with the actual lab number
    secrets: inherit
```

#### Omitting `lab_number`

If you omit `lab_number`, the workflow auto-detects the number from the repo name (e.g. `2026-lab_10` → `10`):

```yaml
jobs:
  comment:
    uses: DataScience4Psych/actions/.github/workflows/auto-comment.yml@main
    secrets: inherit
```

The generated comment will include a link to
`https://github.com/DataScience4Psych/YEAR-lab_NN/issues`, where `YEAR` is
the current calendar year and `NN` is the zero-padded lab number.

---

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

## Overriding the Lab

By default the workflow auto-detects which lab to grade from the student's repo name (e.g. `lab-02-plastic-waste` → `lab02`), falling back to the submission repo name.  
If a student's repo is not named correctly you can override the lab in several ways:

### 1. Comment override (recommended)

Post a comment on the grading issue with the lab identifier directly after `/grade` or `/regrade`:

```
/grade lab02
/regrade lab-02
```

The first lab-like token **containing a number** on the first line of the comment is used (case-insensitive, hyphens/underscores accepted, e.g. `lab02`, `lab-2`, `lab_03`).

### 2. Issue body override

Include a line **starting** with `lab:` in the issue body (the line must begin with `lab:`):

```
lab: lab02
```

The `lab:` line may appear anywhere in the issue body as long as it starts the line.
No lab number means no override (a number is required, e.g. `lab02`, `lab-2`).

### 3. Workflow dispatch override

Trigger the workflow manually from the **Actions** tab and fill in the **Override which lab to grade** input (e.g. `lab02`).

### 4. Reusable workflow input

If calling this workflow from another repo, pass the `lab` input:

```yaml
jobs:
  grade:
    uses: DataScience4Psych/actions/.github/workflows/classroom.yml@main
    with:
      lab: lab02
    secrets: inherit
```
