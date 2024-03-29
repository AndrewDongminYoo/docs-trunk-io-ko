# Advanced Settings

Explanation of settings for states, timeouts, concurrency, and branch protection.

We offer some knobs and dials when configuring Merge.

All of the following settings are specific to individual Merge Queues, and can be applied can be applied at [app.trunk.io](https://app.trunk.io) in the `Settings > Repositories > Repo-Name` page.

## Merge Queue States

You can change the state of your Merge Queue, which will affect behavior around PRs entering the queue and merging.

PRs will always continue testing no matter what state the Merge Queue is in.

Below are the possible different states:

| State      | Will PRs Enter the Queue? | Will PRs Merge After Testing? |
| :--------- | :------------------------ | :---------------------------- |
| `Running`  | Yes ✅                     | Yes ✅                         |
| `Paused`   | No ❌                      | No ❌                          |
| `Draining` | No ❌                      | Yes ✅                         |

There is an additional state, `Switching Modes`, that functions exactly like `Draining`.

The Merge Queue enters this state when you switch the mode of the queue while PRs are still testing.

## Timeout for Tests to Complete

Configure how long a PR's test can run before auto-cancelling while testing in the Merge Queue.

If a long-running test is detected, Merge will automatically cancel the test.

If you're unsure about what timeout to set, you can use our [CI Analytics](https://trunk.io/products/ci-analytics) to analyze the runtime of your CI.
For example, assuming a timeout of 4 hours:

- At 3:00, Bob submits PR 456 to the MergeQueue.
- At 3:05, PR 456 starts testing using Bob's CI system.
- At 7:05, Trunk cancels PR 456, since PR 456 is still testing.

## Concurrency

Configure how many PRs may test in parallel.

A larger number may increase throughput, since more PRs are tested in parallel, but at the expense of CI, since more jobs are running in parallel.

When the queue is at capacity, PRs will still be submitted to it, but they will not begin testing until a PR leaves the queue.

If your testing workload contains some flaky tests, a deeper queue (i.e. a higher concurrency) may struggle.

Running Merge in Parallel mode can help with this, as it will reduce the average depth of your merge queue, since all PRs won't be queued directly behind each other.
For example, assuming a concurrency of 3:

- At 12:00, Alice submits PR 1000 to the MergeQueue, and it starts testing.
- At 12:05, Bob submits PR 888 to the MergeQueue, and it starts testing.
- At 12:10, Charlie submits PR 777 to the MergeQueue, and it starts testing.
- At 12:15, Alice submits PR 1001 to the MergeQueue. Tests do not start, because the MergeQueue is at its concurrency limit.

## Branch Protection

Trunk Merge, since it will eventually merge your PR on GitHub, is still **bound by any protection rules set in GitHub** that affect the branch Trunk Merge will merge into or that affect your Trunk Merge branch.

For example, if a PR requires at least one review to merge, then Trunk Merge would display `'not mergable GitHub yet'` until that PR has a review.

If you have trouble with merge queueing PRs, check if there is any kind of additional branch protection set up on your repo.

Existing branch protection rules must be changed in order to **not** protect branches in the form of `trunk-temp/*` and `trunk-merge/*` .

If either of those branches are considered protected in any way according to GitHub (e.g., if there is a `*/*` branch protection rule), then Merge will not be able to run tests properly due to GitHub permission errors.

If there are any questions or help is needed, reach out on our questions or help is needed, reach out on our [community slack](https://slack.trunk.io/)!
