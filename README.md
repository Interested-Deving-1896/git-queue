# git-queue

[![Built with Ona](https://ona.com/build-with-ona.svg)](https://app.ona.com/#https://github.com/Interested-Deving-1896/git-queue) [![KDE Eco](https://img.shields.io/badge/KDE%20Eco-certified-brightgreen?logo=kde&logoColor=white&style=flat-square)](https://eco.kde.org/) [![Blue Angel](https://img.shields.io/badge/Blue%20Angel-DE--UZ%20215-0055a4?style=flat-square)](https://www.blauer-engel.de/en/certification/criteria) [![Energy](https://api.green-coding.io/v1/ci/badge/get?repo=Interested-Deving-1896%2Fgit-queue&branch=main&workflow=eco-audit.yml)](https://metrics.green-coding.io/ci-index.html)


<!-- AI:start:what-it-does -->
_Description pending._
<!-- AI:end:what-it-does -->

## Architecture

<!-- AI:start:architecture -->
_Architecture documentation pending._
<!-- AI:end:architecture -->

## Install

<!-- Add installation instructions here. This section is yours — the AI will not modify it. -->

```bash
git clone https://github.com/Interested-Deving-1896/git-queue.git
cd git-queue
```

## Usage


It works on Linux, macOS and Windows [virtual environments](https://help.github.com/en/articles/virtual-environments-for-github-actions#supported-virtual-environments-and-hardware-resources).

> IMPORTANT: It has only be tests on Linux. You can contribute [testing it on other operating systems](https://github.com/Nautilus-Cyberneering/git-queue/issues/49).

The action has 3 different commands (specified by the input `action`):

- `create-job`: it allows you to create a new job with any payload.
- `start-job`: it allows the workflow to create a commit when hte job process starts.
- `finish-job`: it allows the workflow to mark the nob as finished.

And one query (also specified by the input `action`):

- `next-job`: it returns the next pending to process job.

You should:

- Create a new Job (`create-job`) and push it immediately to the `main` branch (or whatever your PR target branch is).
- Later, a worker workflow can ask the queue for the next job (`next-job`). The first commit should be the starting commit (`start-job`) and the latest one the finishing commit (`finish-job`).

And the end of the process your `git log` output should be like this:

![Sequence diagram](./docs/images/git-log-screenshot.png)

Sample workflow:

```yaml
name: your workflow

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - name: Set up git committer identity
        run: |
          git config --global user.name 'github-actions[bot]'
          git config --global user.email 'github-actions[bot]@users.noreply.github.com'

      - name: Install dependencies and build
        run: yarn install && yarn build && yarn package

      - name: Create a temp git dir
        run: |
          mkdir ${{ runner.temp }}/temp_git_dir
          cd ${{ runner.temp }}/temp_git_dir
          git config --global init.defaultBranch main
          git init
          git status

      - name: Create new job
        id: create-job
        uses: Nautilus-Cyberneering/git-queue@v1
        with:
          git_repo_dir: ${{ runner.temp }}/temp_git_dir
          queue_name: 'library update - library-aaa'
          action: 'create-job'
          job_payload: '{"field": "value", "state": "pending"}'

      - name: Mark job as started
        id: start-job
        if: ${{ steps.create-job.outputs.job_created == 'true' }}
        uses: Nautilus-Cyberneering/git-queue@v1
        with:
          git_repo_dir: ${{ runner.temp }}/temp_git_dir
          queue_name: 'library update - library-aaa'
          action: 'start-job'
          job_payload: '{"field": "value", "state": "started"}'

      - name: Mutual exclusion code
        if: ${{ steps.create-job.outputs.job_created == 'true' }}
        run: echo "Running the job that requires mutual exclusion"

      - name: Get next job
        id: get-next-job
        if: ${{ steps.create-job.outputs.job_created == 'true' }}
        uses: Nautilus-Cyberneering/git-queue@v1
        with:
          git_repo_dir: ${{ runner.temp }}/temp_git_dir
          queue_name: 'library update - library-aaa'
          action: 'next-job'

      - name: Mark job as finished
        id: finish-job
        if: ${{ steps.create-job.outputs.job_created == 'true' }}
        uses: Nautilus-Cyberneering/git-queue@v1
        with:
          git_repo_dir: ${{ runner.temp }}/temp_git_dir
          queue_name: 'library update - library-aaa'
          action: 'finish-job'
          job_payload: '{"field": "value", "state": "finished"}'

      - name: Show new commits
        run: |
          cd ${{ runner.temp }}/temp_git_dir
          git show --pretty="fuller" --show-signature ${{ steps.create-job.outputs.job_commit }}
          git show --pretty="fuller" --show-signature ${{ steps.start-job.outputs.job_commit }}
          git show --pretty="fuller" --show-signature ${{ steps.finish-job.outputs.job_commit }}
```

## Configuration

<!-- Document configuration options here. This section is yours — the AI will not modify it. -->

## CI

<!-- AI:start:ci -->
_CI documentation pending._
<!-- AI:end:ci -->

## Mirror chain

<!-- AI:start:mirror-chain -->
This repo is maintained in [`Interested-Deving-1896/git-queue`](https://github.com/Interested-Deving-1896/git-queue) and mirrored through:

```
Interested-Deving-1896/git-queue  ──►  OpenOS-Project-OSP/git-queue  ──►  OpenOS-Project-Ecosystem-OOC/git-queue
```

Changes flow downstream automatically via the hourly mirror chain in
[`fork-sync-all`](https://github.com/Interested-Deving-1896/fork-sync-all).
Direct commits to OSP or OOC are detected and opened as PRs back to `Interested-Deving-1896`.
<!-- AI:end:mirror-chain -->

## Contributors

<!-- AI:start:contributors -->
_Contributors pending._
<!-- AI:end:contributors -->

## Origins

<!-- AI:start:origins -->
_Original project — no upstream influences recorded._
<!-- AI:end:origins -->

## Resources

<!-- AI:start:resources -->
_No additional resource files found._
<!-- AI:end:resources -->

## Accessibility

<!-- AI:start:accessibility -->
This repo uses automated accessibility auditing via `check-accessibility.yml`.

Checks include: CODEOWNERS ownership coverage, README screen-reader compatibility,
WCAG 2.1 AA HTML compliance, audio overview (espeak-ng), and Braille output (liblouis).




Run the [Check Accessibility](https://github.com/Interested-Deving-1896/git-queue/actions/workflows/check-accessibility.yml)
workflow to generate the first report and accessibility artifacts.
See [DOCS/accessibility.md](https://github.com/Interested-Deving-1896/git-queue/blob/main/DOCS/accessibility.md) for the full reference.
<!-- AI:end:accessibility -->

## License

<!-- AI:start:license -->
[MIT](https://github.com/Interested-Deving-1896/git-queue/blob/main/LICENSE) © 2026 [Interested-Deving-1896](https://github.com/Interested-Deving-1896)
<!-- AI:end:license -->
