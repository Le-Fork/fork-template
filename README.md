# fork-template

This repository provides a reusable GitHub Actions workflow that keeps a fork’s default branch in sync with its upstream repository on a schedule or manual trigger.

## How It Works

**Listener (sync-upstream.yml)**  
- Runs on a cron schedule or when manually triggered.  
- Checks out the fork’s default branch.  
- Adds the specified upstream remote.  
- Fetches and merges the latest commits from upstream.  
- Pushes the updated branch back to the fork.

## Setup for a New Fork

1. **Add the workflow**  
   Place `sync-upstream.yml` in your fork under `.github/workflows/`.

2. **Customize the upstream**  
   In the `Add upstream remote` step, update the URL to your upstream repo:
   ```bash
   git remote add upstream https://github.com/ORIGINAL-OWNER/ORIGINAL-REPO.git
   ```
   Replace `ORIGINAL-OWNER/ORIGINAL-REPO` with the repository you forked.

3. **Ensure repository settings**  
   - The repository must be public.  
   - The workflow runs on your default branch (e.g., `main` or `master`).

## Organization Actions Settings

To allow this workflow to run, your organization’s Actions policy must permit the **actions/checkout@v4** action and first-party workflows:

1. Go to **Organization Settings → Actions → General → Policies**.  
2. Select **Allow Le-Fork, and select non-Le-Fork, actions and reusable workflows**.  
   Note: you should change Le-Fork with your organization's name.
3. Under **Allow actions created by GitHub**, ensure it is checked.  
4. Under **Allow actions by Marketplace verified creators**, ensure it is checked.  
5. In the **“Allow or block specified actions and reusable workflows”** textbox, list exactly:
   ```
   actions/checkout@v4
   ```

## Triggering the Sync

- **Scheduled sync**: The workflow’s cron schedule runs daily at 05:00 UTC by default.  
- **Manual trigger**: Go to **Actions → Sync from upstream → Run workflow**.

Example schedule in `sync-upstream.yml`:
```yaml
on:
  schedule:
    - cron: '0 5 * * *'   # daily at 05:00 UTC
  workflow_dispatch:     # manual trigger
```

## Permissions

Your workflow requires permission to push changes:
```yaml
permissions:
  contents: write
```
In **Settings → Actions → General**, ensure **Read and write permissions** is enabled. If it’s greyed out, ask an administrator to grant write access.

## Tips

- Always keep the upstream URL in the workflow updated if you ever change the fork’s source.  
- Each fork only needs this listener workflow; there’s no central dispatcher required unless you want to automate across multiple forks.
