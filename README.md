# fork-template

This repository provides a reusable GitHub Actions workflow that keeps a fork's default branch in sync with its upstream repository on a schedule or manual trigger.

## How It Works

**Listener (sync-upstream.yml)**  
- Runs on a cron schedule or when manually triggered.  
- Checks out the fork's default branch.  
- Adds the specified upstream remote.  
- Fetches and merges the latest commits from upstream.  
- Pushes the updated branch back to the fork.

## Setup for a New Fork

1. **Create a Personal Access Token (PAT)**  
   This step is required to allow the workflow to push changes, especially when merging workflow files from upstream.
   
   - Go to your GitHub account → **Settings → Developer settings → Personal access tokens → Tokens (classic)**.
   - Click **"Generate new token (classic)"**.
   - Give it a name (e.g., `sync-workflow-pat`).
   - Select these scopes:
     - `repo` (full control of private repositories)
     - `workflows` (update GitHub Action workflows)
   - Click **"Generate token"** and **copy the token immediately** (you won't see it again).

2. **Add the PAT as an organization secret** (recommended for multiple forks)  
   - Go to **Organization → Settings → Secrets and variables → Actions**.
   - Click **"New organization secret"**.
   - Name: `WORKFLOW_PAT`
   - Value: paste your Personal Access Token.
   - Under **Repository access**, select **"All repositories"** (or specific ones).
   - Click **"Add secret"**.

   *Alternatively*, add it as a repository secret in each fork under **Repository → Settings → Secrets and variables → Actions**.

3. **Add the workflow**  
   Place `sync-upstream.yml` in your fork under `.github/workflows/`.
   
   Make sure the checkout step includes the token:
   ```
   - name: Check out repository
     uses: actions/checkout@v4
     with:
       fetch-depth: 0
       token: ${{ secrets.WORKFLOW_PAT }}
   ```

4. **Customize the upstream**  
   In the `Add upstream remote` step, update the URL to your upstream repo:
   ```
   git remote add upstream https://github.com/ORIGINAL-OWNER/ORIGINAL-REPO.git
   ```
   Replace `ORIGINAL-OWNER/ORIGINAL-REPO` with the repository you forked.

5. **Ensure repository settings**  
   - The repository must be public.  
   - The workflow runs on your default branch (e.g., `main` or `master`).

## Organization Actions Settings

To allow this workflow to run, your organization's Actions policy must permit the **actions/checkout@v4** action and first-party workflows:

1. Go to **Organization Settings → Actions → General → Policies**.  
2. Select **Allow Le-Fork, and select non-Le-Fork, actions and reusable workflows**.  
   Note: you should change Le-Fork with your organization's name.
3. Under **Allow actions created by GitHub**, ensure it is checked.  
4. Under **Allow actions by Marketplace verified creators**, ensure it is checked.  
5. In the **"Allow or block specified actions and reusable workflows"** textbox, list exactly:
   ```
   actions/checkout@v4
   ```

## Triggering the Sync

- **Scheduled sync**: The workflow's cron schedule runs daily at 05:00 UTC by default.  
- **Manual trigger**: Go to **Actions → Sync from upstream → Run workflow**.

Example schedule in `sync-upstream.yml`:
```
on:
  schedule:
    - cron: '0 5 * * *'   # daily at 05:00 UTC
  workflow_dispatch:      # manual trigger
```

## Permissions

Your workflow requires permission to push changes:
```
permissions:
  contents: write
```
In **Settings → Actions → General**, ensure **Read and write permissions** is enabled. If it's greyed out, ask an administrator to grant write access.

## Tips

- Always keep the upstream URL in the workflow updated if you ever change the fork's source.  
- Each fork only needs this listener workflow; there's no central dispatcher required unless you want to automate across multiple forks.
- The PAT approach works for all repositories, including those with branch protections or strict workflow file restrictions.
