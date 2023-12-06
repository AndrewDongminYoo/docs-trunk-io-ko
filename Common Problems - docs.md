## 

My PR is testing in my Merge Queue but no tests results are showing?[](https://docs.trunk.io/merge/common-problems#my-pr-is-testing-in-my-merge-queue-but-no-tests-results-are-showing)

Solution: Most likely you did not set up the required status checks to trigger for `trunk-merge/` branches. It is also possible that your CI provider just randomly never started testing on the Trunk Merge branch, even after setting the required status checks to trigger. To assist with this, you can [configure a testing timeout](https://docs.trunk.io/merge/configuration#timeout-for-tests-to-complete).

## 

My PR appears to be ready but isn't entering the Merge Queue?[](https://docs.trunk.io/merge/common-problems#my-pr-appears-to-be-ready-but-isnt-entering-the-merge-queue)

First, check the Trunk UI where it shows what Trunk is waiting on before putting your PR into the merge queue. **(screenshot)**

Next, if something on that page doesn't look right (for example, it says that GitHub is still checking the merge-ability of the PR), comment `/trunk merge` again in the PR.

## 

My PR is constantly failing when it starts testing because of "GitHub errors".[](https://docs.trunk.io/merge/common-problems#my-pr-is-constantly-failing-when-it-starts-testing-because-of-github-errors-.)

Most likely you have a branch protection rule that is affecting Merge branches. For example, the wild card rule `*/*` applies to `trunk-merge/...`. The Trunk GitHub app does not have admin privileges, so it fails to do some actions on protected branches. To resolve, you must remove this rule or reach out to Trunk on our community Slack if that is not possible.

## 

My PR is constantly failing when attempting to merge it[](https://docs.trunk.io/merge/common-problems#my-pr-is-constantly-failing-when-attempting-to-merge-it)

The two most likely problems are that you are restricting **who can merge**, or that you have **disabled squash merges** into your repo. Trunk Merge needs to use squash merges. To fix this turn on `'allow squash merges'` for this repo in your GitHub setup.

## 

How do I know if I'm running a legacy merge queue?[](https://docs.trunk.io/merge/common-problems#how-do-i-know-if-im-running-a-legacy-merge-queue)

Look for the 'Graph' tab in the UI. If you can see it then you are using the new version.