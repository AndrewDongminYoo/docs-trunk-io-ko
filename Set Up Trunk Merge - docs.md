How to set up Trunk Merge for your project

## 

Connect your Trunk Organization to GitHub[](https://docs.trunk.io/merge/set-up-trunk-merge#connect-your-trunk-organization-to-github)

Sign up at [app.trunk.io](https://app.trunk.io/), create a Trunk organization, and connect it to your GitHub repositories. If your repository is already connected to your Trunk organization, this step can be skipped.

Select the repository you would like to use and click **Get Started.**

![](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2Fgit-blob-c4e88ab41a1453a4056615b7599b410c54e78a87%2Fimage.png?alt=media)

## 

Set Up Trunk Merge[](https://docs.trunk.io/merge/set-up-trunk-merge#set-up-trunk-merge)

-   1.
    
    Go to the Merge tab. You will see the "Set Up Trunk Merge" page. From there, you can specify:
    
    -   1.
        
        The name of the branch that Trunk Merge should help manage merging PRs into
        
    
    -   2.
        
        The number of Pull Requests that Merge can test at the same time
        
    
    -   3.
        
        The [mode](https://docs.trunk.io/merge/configuration#single-parallel-mode) that Trunk Merge Queues will start in
        
    

![](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2Fceeh6VUbrAgRSaMPIZv0%2Fimage.png?alt=media&token=a4835ef9-542d-4c81-9229-17c74309a046)

-   2.
    
    Tell Trunk Merge how to determine whether a pull request can be merged by specifying the name of the [GitHub status checks](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/collaborating-on-repositories-with-code-quality-features/about-status-checks) or [jobs](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions#jobs) that must pass in the `.trunk/trunk.yaml` in your repository.
    

\- Unit tests and test coverage

\# Add more required statuses here

-   3.
    
    Ensure the checks and jobs in `merge.required_statuses` from `.trunk/trunk.yaml` specified in step 2 run whenever Trunk Merge tests a PR. Trunk Merge creates branches with the prefix `trunk-merge/` in order to test PRs, so this means configuring your CI provider to run then whenever a branch with that prefix is pushed to.
    

For GitHub Actions, that'll mean setting up a `push`\-triggered workflow, filtered to `trunk-merge/**` branches, like so:

name: Run Required Checks

run-name: PR Checks for ${{ github.ref\_name }}

\# Trigger jobs whenever Trunk Merge tests a PR using a \`trunk-merge/\` branch

\# "Trunk Check" is specified in merge.required\_status above

uses: actions/checkout@v3

\# "Unit tests & test coverage" is specified in merge.required\_status above

name: Unit tests & test coverage

uses: actions/checkout@v3