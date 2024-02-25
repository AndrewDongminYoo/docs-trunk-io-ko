---
description: >-
  See an overview of your CI Statistics then dig deep into individual workflows,
  jobs, and runs.
---

# Using the Analysis UI

When you first select a repo in CI Analytics, you will see the **Overview** screen which contains charts and tables to give you a high-level view of the state of your CI system. The charts show the aggregate _Average Daily Jobs per Hour_, _Total CI Time_, and _Wait Time per day_ over the selected time period. You can adjust the time period using the selector just below the first chart on the left.

<figure><img src="https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2FQhNyVglnRLnRlYBMWIdM%2FScreenshot%202024-01-11%20at%2012.50.00%E2%80%AFPM.png?alt=media&token=54a7f2d8-fc8d-459f-9c9e-782ebd81bfff" alt="CI Analytics Overview"><figcaption><p>CI Analytics Overview</p></figcaption></figure>

The bottom half of the dashboard shows aggregate statistics for each of your CI workflows _over the selected time period_. The columns are as follows:

| Column         | Description                                                                                                                                      |
| :------------- | :----------------------------------------------------------------------------------------------------------------------------------------------- |
| Workflows      | Name of the workflow, a link to the `yaml` file on GitHub defining the workflow, and a link to view all of the runs for that workflow.           |
| Run history    | Chart for the last 60 runs of the workflow. Hover over a bar in the chart to see details                                                         |
| Failure Rate   | How often the workflow failed during the selected time period.                                                                                   |
| P50 Duration   | Average (50th percentile) duration of the workflow during the selected time period. This shows the time of the _typical_ run.                    |
| P95 Duration   | 95th percentile duration of the workflow during the selected time period. This shows the time of the _outlier_ runs that took longer than usual. |
| Total Duration | Total amount of time these runs took all together, often correlated with your server costs.                                                      |
| Total Runs     | Total number of runs performed (both successful and failed) during the selected time period.                                                     |

### Filtering and sorting your data

Your data in CI Analytics is aggregated in the following hierarchy - Workflow -> Jobs -> Steps. At each level of the data hierarchy, you see an aggregation of the underlying runs that match your selected filter. At any time, you can dive into the underlying data by selecting `View runs` which will show the [**Runs View**](using-the-analysis-ui.md#runs-view) for that dataset.

The set of runs that are being aggregated and presented can be filtered by the following criteria:

#### By Date

Work can be filtered by common ranges: 7 days, 14 days, 30 days, 3 months, 6 months, and any custom date range. Date filters can be applied in the date selector in the nav bar or by hovering over a data point in the chart and selecting `Zoom In`.

#### By Branch Type

| Branches     | Description                                                                                     |
| :----------- | :---------------------------------------------------------------------------------------------- |
| All          | Shows work with any branch filtering                                                            |
| main         | Work that is performed against your main or master branch.                                      |
| merge        | Work that is performed by a merge queue service (including trunk merge and GitHub's MergeQueue) |
| pull request | Work attributable to the testing of a pull request                                              |
| dependabot   | Work attributable to the testing of a PR created by Dependabot                                  |
| renovate     | Work attributable to the testing of a PR created by Renovsate                                   |

#### By Conclusion

The conclusions filter allows you to look exclusively at work based on its reported termination state: success, failure, canceled, time out, skipped, etc... If you are trying to understand the performance of your CI tasks, you will likely want to filter by success. Failures can occur at any time, but the success path is a more reliable view of the expected runtime of your system.

#### By Name

You can filter your data by _name_ (workflow, job, step) in the search box at the bottom-right of the chart view.

### Exploring your data by Workflow / Job / Step

From the Analytics Overview, you can dive into the hierarchy of your CI data. At each level of the hierarchy, you will see aggregated performance information and can dive deeper into the contents.

The top of the view shows a chart of aggregate statistics for the filtered data set. You can customize this view to bucketize this data by week/day/hour as applicable. Hovering over points in the chart will show details for that time slice.

If you click on one of the data buckets or points on the graph, you can Zoom into that period of data or view the underlying runs.

![ ](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2FmSdPeURwY7zOovtL75TB%2Fimage.png?alt=media&token=a900eec9-32ad-467a-8b60-a4db8efcfa3c)

**Tags**: Aggregation at the job level for GitHub workflows will implicitly shard against the 'runs-on' field of the GitHub workflow. For example, if a job was running on`ubuntu-latest` and later switched to a different runner set - a unique row will be created for each distinct runs-on match.

When there is a name collision with different underlying tags, the unique tag will be surfaced in the view. This makes it possible, for example, to compare a runner's impact on your system's performance.

### Runs View

<figure><img src="https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2FM55RhAz7PDrRUsRymuEL%2Fworkflow-jobs-steps-list.png?alt=media&token=f413c5b0-e444-467a-a514-33b8152ea92e" alt="screenshot of the Runs View"><figcaption><p>Runs View</p></figcaption></figure>

The **Runs View** allows you to explore the underlying data from an aggregated view. Each row represented a single execution, including its Start time, run time, waiting time, and conclusion.

Where available, the rows will include the Pull Request associated with this work, the commit it ran against, links to the associated logs, and the specific machine that the job ran on.
