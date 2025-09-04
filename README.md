# fork-template

This repository contains a reusable GitHub Actions workflow that keeps a fork’s default branch in sync with its upstream repository whenever a dispatch event is received.
How It Works

    Dispatcher
    A central dispatcher workflow (in your org’s .github repo) sends a repository_dispatch event of type upstream-sync to each fork.

    Listener (sync-upstream.yml)
    This workflow listens for that event, checks out the fork’s default branch, merges the latest commits from the specified upstream, and pushes the result back to the fork.

Setup for a New Fork

    Add this workflow
    Place sync-upstream.yml at .github/workflows/sync-upstream.yml in your fork.

    Customize the upstream
    Edit the Add upstream remote step:

    text
    git remote add upstream https://github.com/ORIGINAL-OWNER/ORIGINAL-REPO.git

    Replace ORIGINAL-OWNER/ORIGINAL-REPO with the upstream repository you forked.

    Ensure Public & Default Branch

        The repository must be public.

        The workflow uses your default branch (main/master).

Triggering the Sync

    The central dispatcher (in .github) should be configured to send:

text
event-type: upstream-sync

Manually trigger via Actions → Run workflow or set up a cron schedule in the dispatcher:

    text
    on:
      schedule:
        - cron: '0 5 * * *'  # daily at 05:00 UTC

Permissions

    The workflow requires contents: write to push merged changes back to the fork.

Tips

    Keep the upstream URL updated whenever you change the fork’s source.

    Use your org’s .github repo to manage and update the central dispatcher—no need to touch individual forks after initial setup.
