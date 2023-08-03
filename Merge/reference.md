# Reference

## Submitting Pull Requests

Users can submit pull requests to Merge in two ways: using the Trunk CLI or via a comment in the GitHub pull request UI.

### Submitting with Trunk CLI

To submit a pull request to Merge with the Trunk CLI, a user must first be logged in. Run `trunk login` and follow the prompts to log in. After logging in, pull requests can be submitted by PR number: `trunk merge {pr-number}`.

![](https://files.readme.io/3ba842c-Screen\_Shot\_2022-09-11\_at\_10.43.28\_PM.png)

### Submitting via a comment in the GitHub pull request UI

Checking the checkbox in Trunk Merge's GitHub comment or commenting `/trunk merge` in your own comment, will submit it to be merged.

![](https://files.readme.io/b6513d0-image.png)

### Stacked PR Support

You can `/trunk merge` any PRs in a stack, in any order, and they will wait to proceed until their parent PR has merged.

## Cancelling Pull Requests

### Cancelling with the Trunk CLI

Similar to submitting new pull requests, a user can remove an existing pull request from the queue by running `trunk cancel {pr-number`

### Cancelling a comment in the GitHub pull request UI

A pull request can be removed from the queue by commenting `/trunk cancel`

## Parallelism

To keep pull request throughput high, Merge can test multiple pull requests simultaneously. This is done by creating a branch for a pull request from the `HEAD` of the target branch along with the changes from the pull request and any that are ahead of it. This branch is the predicted state of the repository if the pull requests ahead of this one pass all the required checks.

To configure how many pull requests are tested in parallel, navigate to Settings > Repositories > Repo-Name and update the value.

![](https://files.readme.io/d2bc2dc-Screen\_Shot\_2022-09-11\_at\_11.08.36\_PM.png)

## Required Status Checks

Merge uses [GitHub status checks](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/collaborating-on-repositories-with-code-quality-features/about-status-checks) to determine whether a pull request can be merged. Whenever a pull request passes branch protection rules, Merge will wait for the required status checks to be posted to the test branch. You can configure these checks by adding them to the `trunk.yaml` configuration file in your repository. For example:

```yaml
version: 0.1
cli:
  version: 0.17.0-beta
merge:
  required_statuses:
    - Trunk Check
    - Unit tests & test coverage
    - Integration tests
```

If no required checks are specified for your repository, Merge will merge pull requests once they have passed branch protection rules.

## Running Required Checks

In order to merge a pull request, the test branch created by Merge must be passing all required [status checks](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/collaborating-on-repositories-with-code-quality-features/about-status-checks). When a pull request is ready to be tested, Merge creates a temporary test branch with the name prefix `trunk-merge/` You should configure your CI jobs to run when a branch is created with the prefix `trunk-merge/`. Once those jobs post the status checks to GitHub, Merge will either pass or fail the pull request.

## GitHub Branch Protection Rules

Merge monitors [GitHub's branch protection rules](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/defining-the-mergeability-of-pull-requests/about-protected-branches#about-branch-protection-rules) and doesn't push a test branch for a pull request until it has passed all rules and is mergeable. You can find more information about why a pull request hasn't entered the queue by clicking on it in the Merge UI at [app.trunk.io](https://app.trunk.io).

![](https://files.readme.io/a2ccbf1-Screen\_Shot\_2022-09-11\_at\_11.15.15\_PM.png)

Most repository maintainers will want to use the GitHub branch protection settings to limit who can merge pull requests. By restricting who can merge to admins and the Github Trunk App, you can prevent contributors from circumventing Merge and merging code directly.\
Merge does account for admins needing to merge pull requests directly, effectively skipping Merge entirely. If a pull request is submitted without using Trunk Merge, the service will restart tests on all pull requests with the updated target branch.

## Pausing your queue

You may occasionally need to prohibit pull requests from merging for some maintenance or emergency intervention. Admins can pause the queue by running `trunk merge pause` or toggling the state in the application settings. Running `trunk merge resume` or toggling the setting back to running will allow Merge to pick up where it left off. Keep in mind, if any pull requests were merged while the service was paused, pull requests in the queue will be restarted.

## Canceling a queue item’s job

When Merge creates a branch, it triggers a job within your specified CI provider. Merge cleans up after itself and deletes the created `trunk-merge/` branch when the queue item has been merged, is canceled, or fails. When this occurs, just like you monitor for the branch being pushed to, you may also want to monitor for branch deletion in order to cancel those jobs (if applicable). If the results of these jobs for a failed queue item are unnecessary for your team, canceling these jobs can help cut down on costs.

It is important to note that branch deletion will trigger a new `on_push` event from GitHub. You can configure your systems to either handle the case where `on_push` is triggered and `deleted: true` is marked within the payload or explicitly monitor for a branch deletion event for Merge branches.

Each CI system will handle this slightly differently, but below are a few references of how to handle it. These are meant as a reference point and are by no means the only way to handle job cancellations.

### GitHub

We handle this one for you. When the job is canceled by a Merge user, we automatically cancel the triggered workflow for you.

### Buildkite

Buildkite does not support currently [responding to branch deletion events](https://forum.buildkite.community/t/trigger-pipeline-on-branch-deletion/1567/2) directly and recommends that users create a lightweight github action that handles it. This action should be configured such that it responds to unsupported events and forwards that request to buildkite. This job should use the Buildkite API to trigger on the Merge branch deletion. It will then list the build for the Merge branch being deleted and then cancel the build.

### Jenkins

Jenkins can be configured in various ways to handle job cancellation. One way is to have a separate pipeline that is configured on GitHub to trigger on Merge branch deletion. Once triggered, it can list all running jobs and cancel any that reference the same Merge branch that triggered the pipeline.