# Set Up Trunk Merge

How to set up Trunk Merge for your project.

### Connect your Trunk Organization to GitHub

Sign up at [app.trunk.io](https://app.trunk.io/), create a Trunk organization, and connect it to your GitHub repositories.

If your repository is already connected to your Trunk organization, you can skip this step.

Select the repository you would like to use and click **Get Started.**

![ ](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2Fgit-blob-c4e88ab41a1453a4056615b7599b410c54e78a87%2Fimage.png?alt=media)

### Set Up Trunk Merge

Go to the Merge tab.

You will see the "Set Up Trunk Merge" page.

From there, you can specify:

1. The name of the branch that Trunk Merge should help manage merging PRs into.
2. The [number](https://docs.trunk.io/merge/set-up-trunk-merge/advanced-settings#concurrency) of Pull Requests that Merge can test at the same time.
3. The [mode](https://docs.trunk.io/merge/set-up-trunk-merge/configuration#single-parallel-mode) that Trunk Merge Queues will start in.

![ ](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2Fceeh6VUbrAgRSaMPIZv0%2Fimage.png?alt=media&token=a4835ef9-542d-4c81-9229-17c74309a046)

### Define Required Status Checks For Testing

Trunk needs to know which _status checks_ must pass while testing pull requests in the queue before it can merge a PR into your branch.

Merge can pick up this list of required statuses in one of two ways:

1. from the list of "Require status checks to pass before merging" specified in the [GitHub branch protection](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches#require-status-checks-before-merging) rule for your merge branch (which is particularly useful if you want Merge to track the same status checks that must pass before GitHub can merge a PR), or
2. from the `merge.required_statuses` section of your `.trunk/trunk.yaml` (if you want the list of status checks that must pass before Merge can merge a PR to be different from the required status checks that must pass as a part of GitHub branch protection).

To specify using the `.trunk/trunk.yaml` file, set the `merge.required_statuses` to the name(s) of the [GitHub status checks](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/collaborating-on-repositories-with-code-quality-features/about-status-checks) or [jobs](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions#jobs) that must pass:

```yaml
version: 0.1
cli:
  version: 1.16.0
merge:
  required_statuses:
    - Trunk Check
    - Unit tests & test coverage
    # Add more required statuses here
```

To use GitHub branch protection instead follow GitHub's instructions for [requiring status checks](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches#require-status-checks-before-merging).

Required statuses defined in the `.trunk/trunk.yaml` file take precedence over the GitHub required status checks from branch protection, as Trunk Merge will only pull the list of required statuses from one or the other - it will not combine them.

If neither is defined the merge process will fail.

### Configure a Push Triggered Workflow For Required Status Checks

Trunk Merge creates branches with the prefix `trunk-merge/` in order to test PRs.

To ensure the required statuses Merge should gate on get triggered when it tests PRs, your CI provider must be configured to run the status checks you care about whenever a branch with that prefix is pushed to.

For GitHub Actions, that'll mean setting up a `push`-triggered workflow, filtered to `trunk-merge/**` branches, like so:

```yaml
name: Run Required Checks
run-name: PR Checks for ${{ github.ref_name }}

# Trigger jobs whenever Trunk Merge tests a PR using a `trunk-merge/` branch
on:
  push:
    branches:
      - trunk-merge/**

jobs:
  trunk_check:
    runs-on: ubuntu-latest
    # "Trunk Check" is specified in merge.required_status above
    name: Trunk Check
    steps:
      - name: Checkout
        uses: actions/checkout@v3

  unit_tests:
    runs-on: ubuntu-latest
    # "Unit tests & test coverage" is specified in merge.required_status above
    name: Unit tests & test coverage
    steps:
      - name: Checkout
        uses: actions/checkout@v3

    # Add more steps here.
```

Now you are ready to submit your first PR.

Let's go!

### Submit Pull Requests

Try making a simple change on a branch and submit it as PR in GitHub.

Now trigger Trunk Merge to process this PR using either a comment on the PR in GitHub or using the Trunk CLI.

Comment `/trunk merge` on a pull request

![ ](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2Fgit-blob-16b95a8844ba42c299e19e92a55fecc75831827d%2Fimage.png?alt=media)

```shell
# Authenticate with trunk service
$ trunk login
# Queue pull request for merge
$ trunk merge {pr-number}
```

If you have any problems with merge queueing PRs, take a look at the [branch protection](https://docs.trunk.io/merge/set-up-trunk-merge/advanced-settings#branch-protection) docs.

### Pull Request Processing

Once a PR is submitted to the Merge queue, it will start as _Not Ready_ until all of the required conditions to submit it are met.

Once ready, the Merge Queue will pick it up and run the tests.

Once the tests pass, the PR may still need to wait for upstream PRs in the queue to finish their testing.

Once the remaining upstream PRs are complete, the PR will be merged and then removed from the Merge Queue.

If a PR fails or is canceled then it will go to the failed or canceled state.

Read more about [PR States](https://docs.trunk.io/merge/reference#pr-states).

## Success

Now Trunk Merge is setup with your repo.

Whenever a PR is pushed to your merge branch it will be safely tested and automatically merged when all tests pass, regardless of the order they were pushed in.

### Next Steps

You can configure [parallel mode](https://docs.trunk.io/merge/set-up-trunk-merge/configuration) for potential performance gains, read how to [cancel pull requests](https://docs.trunk.io/merge/set-up-trunk-merge/configuration#submitting-and-cancelling-pull-requests), and setup a [Slack Integration](https://docs.trunk.io/merge/set-up-trunk-merge/integration-for-slack).

If you are using Bazel you may want to [further customize](https://docs.trunk.io/merge/set-up-trunk-merge/merge-+-bazel) it for parallel mode.
