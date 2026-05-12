---
name: Daily Digest
description: Create a weekday GitHub issue that summarizes all open issues and pull requests by label.
on:
  schedule: daily on weekdays
  workflow_dispatch:
permissions:
  contents: read
  issues: read
  pull-requests: read
engine: copilot
strict: true
timeout-minutes: 10
network: defaults
tools:
  github:
    mode: gh-proxy
    toolsets: [default, issues, pull_requests]
safe-outputs:
  mentions: false
  allowed-github-references: []
  create-issue:
    title-prefix: "Daily Digest – "
    close-older-issues: true
    expires: 14
---

# daily-digest

Create a GitHub issue titled `Daily Digest – <YYYY-MM-DD>` for `${{ github.repository }}` every weekday.

Collect all currently open issues and all currently open pull requests in the repository. Treat issues and pull requests as separate resource types when querying so both sets are complete. Include draft pull requests. Exclude closed and merged items.

Format the report as follows:

### Summary
- Date: the digest date in `YYYY-MM-DD` format
- Open issues: total number of open issues
- Open pull requests: total number of open pull requests
- Total open items: combined total

### By label
Group results by label name, sorted alphabetically, with an `unlabeled` section at the end for items that have no labels.

For each label section:
- Show the total number of matching items in that section
- Separate issues from pull requests inside the section
- Use a markdown table with the columns `Type`, `Title`, `Author`, and `Open`
- Link each title directly to the issue or pull request URL
- Show the author as their GitHub username without adding commentary
- Show `Open` as a human-readable age such as `2d`, `5h`, or `3w`

If an item has multiple labels, include it in each matching label section. The totals in the summary must still reflect the unique counts of open issues and open pull requests.

If there are no open issues or pull requests, still create the digest issue and clearly state that the repository has no open items.

Keep the report concise, readable, and factual. Do not add any footer attribution. End with:

**References:** [workflow run](${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }})
