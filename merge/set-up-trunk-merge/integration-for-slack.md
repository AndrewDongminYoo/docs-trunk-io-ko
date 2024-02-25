---
description: Push updates about your queue status to Slack to keep your team informed.
---

# Integration for Slack

Trunk Merge integrates with Slack to push relevant information to your team so you can stay up to date with the status of your pull requests.

## Setup Integration with Slack

After you have Merge set up and running in your repository you can set up Slack notifications.

There are two ways to configure where notifications are delivered: per queue and per user.

You can have all notifications from a specific Merge Queue send to a Slack channel.

You can also have notifications for PRs authored by you, regardless of which queue they are going through, sent as a DM to you.

### Merge Queue Specific Notifications

After you have Merge set up and running in your repository, you can set up your integration with Slack in Settings > Repositories > the specific repository with the Merge Queue.

![ ](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2F1CKPbolV5UO0g7elO6fl%2Fimage.png?alt=media&token=69b2ccce-4f8b-4f26-b622-45c6ee13aae3)

Click "**Connect**" to install the Trunk Slack application and receive Merge notifications in a specific channel.

Once you have authorized the app to post to a channel, you will be redirected to your settings page.

### User Specific Notifications

To have notifications for **PRs you authored** regardless of what Merge Queue they're going through sent **as a DM to you**, you go to `Settings > Account > Connect`

Note: For user specific notifications you **must** have your GitHub account connected to your Slack account.

The button to do this is in the `Settings > Account > Link GitHub Account` section.

![ ](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2F0hLjcY9aOiUhT1EOnmQh%2Fimage-6.png?alt=media&token=6eb6bca8-e242-4498-993b-8d9cd70b70b2)

## Configuring Your Integration with Slack

After you have connected to a Slack channel, you can select which notifications you would like to receive.

By default, all Merge notifications are enabled.

| Notification                                     | Description                                                                                                                                                                                                                                              |
| ------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Merge is paused or resumed                       | Receive a notification when merging has been paused or resumed.                                                                                                                                                                                          |
| Pull request is merged                           | Receive a notification when a pull request is merged into the target branch.                                                                                                                                                                             |
| Pull request is canceled                         | Receive a notification when a pull request is canceled.                                                                                                                                                                                                  |
| Pull request fails                               | Receive a notification when a pull request fails tests or otherwise fails to merge into the target branch.                                                                                                                                               |
| Pull request is merged without using Trunk Merge | Receive a notification when a user merges directly to the target branch without submitting to Trunk Merge. This will cause any in-progress pull requests to be restarted, and typically should only be done in emergencies by designated administrators. |
