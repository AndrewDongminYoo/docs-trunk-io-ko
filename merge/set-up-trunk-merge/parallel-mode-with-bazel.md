# Parallel Mode with Bazel

When using Bazel, Trunk Merge can create parallel merge queues for your PRs.

Leveraging [parallel mode](https://docs.trunk.io/merge#single-mode-vs-parallel-mode) for Trunk Merge is easy for Bazel enabled repos because Bazel already knows the structure of your code and can automatically generate a dependency graph.

Merge can use this information in parallel mode to run your CI tests more efficiently; eliminating checks when Bazel already knows they cannot conflict.

### Setup Trunk Merge

Connect your GitHub repository with Trunk and set up Trunk Merge with [the standard instructions.](https://docs.trunk.io/merge/set-up-trunk-merge)

### Turn on Parallel Mode

Next [enable Parallel Mode](https://docs.trunk.io/merge/set-up-trunk-merge/configuration) in the Trunk Merge UI.

![screenshot of Trunk Merge configuration screen](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2F1CKPbolV5UO0g7elO6fl%2Fimage.png?alt=media&token=69b2ccce-4f8b-4f26-b622-45c6ee13aae3)
_Enabling Parallel Mode_

### Set Up the Github Action

Trunk provides a [Merge GitHub action](https://github.com/trunk-io/merge-action) (workflow) that leverages Bazel to take care of generating the required [impacted targets](https://docs.trunk.io/merge/set-up-trunk-merge/impacted-targets) and uploading them to Merge, taking care of the heavy work for using Parallel mode.

In order for GitHub to communicate with Trunk Merge, it needs to be able to authenticate from the GitHub Action instance to the Trunk web application using your **Trunk Organization API Token**.

#### Get your Organization API Token

#### Store your Organization Token as a GitHub Secret

#### Add merge action with Trunk Org Token

Now add the [`trunk-io/merge-action`](https://github.com/trunk-io/merge-action) action in a new workflow in your repo under `.github/workflows` (eg: `.github/workflows/uploaded_impacted_targets.yaml` )

```yaml
name: Upload Impacted Targets
run-name: Upload Impacted Targets for ${{ github.ref_name }}
on: pull_request

jobs:
  compute_impacted_targets:
    name: Compute Impacted Targets
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        with:
          lfs: true

      - name: Compute Impacted Targets
        uses: trunk-io/merge-action@v1
        with:
          # Use your Trunk repo or org API token to authenticate impacted targets uploads.
          # This secret should be provided as a GitHub secret.
          # See https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions.
          trunk-token: ${{ secrets.TRUNK_ORG_API_TOKEN }}
```

If your Bazel setup is not in the root of your repo then you can add `bazel-workspace-path: your_workspace_path` just below the `trunk-token`.

Commit the new workflow back to your repository to make it active.

Now you can [submit a new pull request and test it](https://docs.trunk.io/merge/set-up-trunk-merge#submit-pull-requests). **Success!**
