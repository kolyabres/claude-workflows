# claude-workflows

Shared Claude Code workflow for kolyabres' repositories: a code review on every
pull request (inline comments, an outcome review that approves or requests
changes) and `@claude` in any issue or pull request thread.

## Use it in a repository

1. Add `.github/workflows/claude.yml`:

   ```yaml
   name: Claude
   on:
     pull_request:
       types: [opened, synchronize, ready_for_review, reopened]
     issue_comment:
       types: [created]
     pull_request_review_comment:
       types: [created]
     pull_request_review:
       types: [submitted]
     issues:
       types: [opened, assigned]
   jobs:
     claude:
       # The ceiling for the called jobs; each narrows it to what it needs.
       permissions:
         contents: read
         pull-requests: write
         issues: write
         id-token: write
         actions: read
       uses: kolyabres/claude-workflows/.github/workflows/claude.yml@v1
       secrets: inherit
       # with:
       #   extra_prompt: "Anything this repository's reviews should know."
   ```

2. Set the token: `gh secret set CLAUDE_CODE_OAUTH_TOKEN --repo kolyabres/<repo>`
   (a personal account has no account-wide secrets).
3. Install the Claude GitHub App on the repository.

## Access

This repository is private. Other private repositories of the same user can
call it only while Settings -> Actions -> General -> Access is set to
"Accessible from repositories owned by the user":

```bash
gh api -X PUT repos/kolyabres/claude-workflows/actions/permissions/access -f access_level=user
```

## Releasing a change

Callers pin `@v1`. Fix on `main`, then move the tag:

```bash
git tag -f v1 && git push -f origin v1
```

A change that needs callers to edit their stub gets `v2` instead.
