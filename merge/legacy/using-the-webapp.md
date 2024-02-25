---
description: Using the webapp with the older legacy Trunk Merge.
---

# Using the Webapp

The Trunk web app has a UI for inspecting the status of pull requests or your queue health.

## Queue Overview

The queue overview shows you which pull requests are currently in flight. \\

![ ](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2Fgit-blob-6a896ce2ab561f3190b58ef54d81ad79fd58fa3b%2Fimage.png?alt=media)

In this view, you can see all the pull requests currently being tested, along with their progress. Merge waits for all [required status checks](reference.md#required-status-checks) to pass before merging a pull request. You can also see any pull requests that have not begun to merge, along with which statuses they are waiting on.

## Failures View

![ ](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2Fgit-blob-300b6b5c5847c38cd70b37d080a1fed94b3b9268%2Fimage.png?alt=media)

Pull requests that fail to merge, or are canceled, do not show in the Queue Overview. Instead, they are viewable by clicking the Failures tab.

## Pull Request Details

Clicking on a pull request in the queue overview will open a sidebar pane with relevant details for the status of that pull request.\\

![ ](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2Fgit-blob-55b3f4715ab26891dc1cbfbda7ac7101ea8c24a3%2Fimage.png?alt=media)

Clicking "View History" will give you a full audit trail for a pull request.

![ ](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2Fgit-blob-2cbaec39be656d647d7e0ea192d3877a5177b429%2Fimage.png?alt=media)

This view displays a timeline for a pull request, including when it was submitted, when it began testing, and details about any time it had to restart tests. For pull requests that failed and had to be resubmitted, you'll also be able to view the timeline of each submission.
