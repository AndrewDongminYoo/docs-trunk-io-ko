---
description: >-
  The web app offers detailed information about pull requests, the state of the
  merge queue, and more.
---

# Using the Merge UI

The web app can be found at [app.trunk.io](https://app.trunk.io).

## Queue Overview

The queue tab provides an overview of the work done by Merge. Merged, testing, and pending PRs should all appear here. Clicking on a row will show the history of that item: click "view more history" to get a better understanding of the item.

![view more history](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2FnJbnA6lfsOCasLYFGPP9%2Fimage.png?alt=media&token=cfc71c35-5817-4410-ae72-068da9848060)
_The "view more history" link navigates you to the details page._

### Pull Request Details

The PR details shows information about a PR, including a link to the PR in GitHub, the history of the PR within Trunk Merge, and what must be done before a PR can be admitted to the queue for PRs that have not entered the queue yet.

When a PR has not been admitted to the queue yet, Trunk Merge waits for:

1. Impacted targets to be uploaded for the PRs current SHA (`Parallel` mode only)
2. The PR to be mergeable according to GitHub. If the PR is not mergeable yet, this most likely means that the PR is not meeting all branch protection rules you have set (for example, not all required status checks have passed yet) or has a merge conflict with the target branch
3. The target branch of the pull request to match the branch that merge queue merges into

![PR readiness details](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2FW9ZvWPt32csGPctP6BTg%2Fimage.png?alt=media&token=42077524-20e9-4eb2-8f67-62caec4a6b2f)
_PR readiness details for a PR that has been submitted but has not yet entered the merge queue. In this example, the queue is waiting for impacted targets to be uploaded for the PR and is also waiting for the PR to be mergeable according to GitHub._

In the screenshot above the PR's base branch matches the Merge branch, but the impacted targets are not yet uploaded, but it is not mergable on GitHub yet.

The PR Details panel has a dropdown menu (labeled "**..."**) with actions. From this menu you can:

1. Remove a PR from the queue. If the PR is "Not Ready", then it will cancel it, preventing it from going into the queue until it is re-queued. If the PR is currently in the queue, it will be removed from the queue, which will restart all PRs that depended on it
2. Re-queue a PR if it is currently not in the queue
3. Download any impacted targets that have been [uploaded](set-up-trunk-merge/impacted-targets.md#generating-impacted-targets) for the PR (uploading impacted targets is only required for [Parallel](configuration.md#parallel-mode) mode, but this option will still show regardless of mode if impacted targets have been uploaded for the PR)

![ ](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2FOgbgOUiFMB9xdForkfYT%2Fimage.png?alt=media&token=fa1727a2-c209-478a-a021-d6a404e448e7)

The "Remove from queue" action will remove the PR from the merge queue. If the PR is "Not Ready", then it will cancel it, preventing it from going into the queue until it is re-queued. If the PR is currently in the queue, it will be removed from the queue, which will restart all PRs that depended on it:

1. ### PR Details

A PR details page will display a complete history of a PR - state transitions, associated test runs, a visual of what's currently in the Merge Queue, etc. The same dropdown menu described above (labeled "...") is on this page as well.

![ ](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2FNDqevRCFNyTfGLaQpf6w%2Fimage.png?alt=media&token=b44b821a-06e6-4613-9cb0-46579ac177fd)

## Failures

A tabulated view of all the items that have failed in the Merge Queue, e.g. due to testing.

![ ](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2FfQB1nfbyNylgfa038E5M%2Fimage.png?alt=media&token=897aa360-a789-468e-949a-adfe1ff13ed9)

## Manually Restarting Failed PRs

Trunk Merge will automatically restart failed PRs when in can under certain conditions (see PR states). Since the restart is usually from a failed PR being removed form the queue, other PRs behind it will also be restarted. If you want to manually restart a PR, you can restart it **_in place_** using the **Restart tests** option of the PR dropdown (labeled "...")

![PR actions menu](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2FzRob0488aQHZ0r4uSePq%2Fpr-restart-menu.png?alt=media&token=6179b013-88cf-4a1d-80c7-e1d47d127876)
_PR actions menu_

There are a couple of reasons you might want to manually restart a PR. First, if a PR ends up in the PENDING_FAILURE state because of something transient like a CI runner disconnecting or flakey tests, you can restart the PR right away instead of waiting for an PRs in front of it to pass or fail.&#x20;

Another reason to restart a PR is if the proper tests don't get kicked off due to a failure in the CI system. For example, if GitHub has an outage and is not triggering workflows or actions properly.&#x20;

## Queue Visualization

The view of all current PRs being tested by Trunk Merge and their respective queues. Each node shown is a pull request, and each edge indicates that the pull request is testing with the item above and depends on it. All edges point towards the target branch; as items merge, the affected queues restructure. If running in `Single` mode, the this will be a single line showing the testing and merging process.

![ ](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2FOoKah5Ecn70I6qY6kg1O%2Fimage.png?alt=media&token=c1cf9090-950b-44a9-a787-21320d17b028)

You can click on any shown PR to navigate to the details page for that PR.
