# GitHub Actions Quickstart

### Requirements

Your CI machine needs to have the following tools installed:

- Bash,
- Curl

You will need to have ports open for:

- api.trunk.io:443
- api.trunk.io:8443

### Create a trunk organization

To use the CI Debugger, you must first create a trunk organization.

See this [document](https://docs.trunk.io/administration/manage-members-and-organizations) for detailed instructions.

### Create a breakpoint in the trunk app

### Setup your Organization API Token

In order for the CI Debugger to communicate with the trunk web app, it needs to be able to authenticate from the GitHub Action instance to the trunk web application.

### Store your Organization Token as a GitHub Secret

### Wrap one of your GitHub steps in a `trunk breakpoint`

Now for the magic 🪄.

Open an existing GitHub Action and wrap one of your existing steps with a `trunk breakpoint` like this [(example here)](https://github.com/trunk-io/debugger-demo/blob/main/.github/workflows/pr.yml):

```yaml
name: Pull Request
on:
  - pull_request

jobs:
  test:
    name: Test
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Testing
        uses: trunk-io/breakpoint@v1
        with:
          breakpoint-id: {INSERT_YOUR_BREAKPOINT_ID} [1]
          run: ./run_tests.sh
          trunk-token: ${{ secrets.TRUNK_TOKEN }} [3]
          org: {INSERT_YOUR_TRUNK_ORGANIZATION} [2]
```
