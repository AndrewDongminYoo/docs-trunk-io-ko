---
description: Merge can be configured in single or parallel mode depending on your needs.
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Parallel Mode

## Single / Parallel Mode

Merge can support two different modes - `Single` and `Parallel` mode. The mode affects how PRs are enqueued when they enter the queue and what is required for a PR to begin testing.

#### Parallel Mode

In this mode, Trunk Merge will dynamically create new merge queues for PRs that affect different parts of your code base. To support this, you will be required to provide a list of impacted targets that result from code changes in the PR (see [Impacted Targets](set-up-trunk-merge/impacted-targets.md) for more details as well as how to generate the list of impacted targets). Trunk Merge will then examine the provided list of impacted targets for all PRs in the existing merge queues and only make your PR depend in the other PRs it actually affects.

For example, the following PRs:

- PR 1 with impacted target list `[src/protos/]`
- PR 2 with impacted target list `[src/user_service/, src/protos/]`
- PR 3 with impacted target list `[docs/]`

would be split into two different queues in `Parallel` mode

![" width="332](merge-flow-parallel.png)

#### Single Mode

In this mode, PRs will always be queued directly behind one another in a first-in first-out fashion, regardless of what parts of code your PR actually affects. Uploading impacted targets is not required in this mode.

For example, the above 3 PRs would look like this in the Merge Queue in `Single` mode

![ ](merge-flow-single.png)

#### Switching Modes

Merge can be swapped between `Single` and `Parallel` mode at any time. If there are no PRs in the merge queue when switching, the switch will be immediate. If there are PRs in the queue, then Merge will go into the `Switching Modes` state, where it'll wait for all currently testing PRs to merge before switching modes. During this time, PRs will not be able to enter the queue.

Switching modes can be done from the `Merge Queue Mode` section of the `Settings > Repositories > repo name > Merge` panel

<figure><img src="./set-up-trunk-merge/enable-parallel-mode.png" alt=""><figcaption><p>enabling parallel mode</p></figcaption></figure>
