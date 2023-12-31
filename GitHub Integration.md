---
description: Automate your code quality enforcement with just a few clicks.
---

# GitHub Integration

Now that you have `trunk check` running on your local computer, your next step is to run trunk automatically in the cloud and share notifications with your whole team. Start by connecting your trunk organization to GitHub.

## Connect your Trunk organization to GitHub

Sign up at [app.trunk.io](https://app.trunk.io), create a Trunk organization, and connect it to your repositories. See our GitHub App permissions [here](./GitHub%20Codespaces.md) if interested.

## Set Up Trunk Check

## Configure Slack Notifications (optional)

## Use it

### Ensure that PRs are free of issues

Check out [this example](https://github.com/trunk-io/plugins/pull/424/checks?check_run_id=15730277425) in our `plugins` repository!

<div data-full-width="true">

<figure><img src="../assets/image (16).png" alt=""><figcaption><p>Trunk Check identifying a security issue in one of Trunk's own repositories</p></figcaption></figure>

</div>

### Explore issues in your repository

<figure><img src="../assets/Screenshot 2023-08-23 173119.png" alt=""><figcaption><p>Trunk Check showing all the issues present in trunk-demo1/sass</p></figcaption></figure>

### Get Slack notifications about new issues in your repository

Not only do our daily scans allow you to browse and triage the issues in your repository, but they can also notify you when new security issues are discovered in packages you already depend on.

<div data-full-width="true">

<figure><img src="../assets/Screenshot 2023-08-23 173252.png" alt=""><figcaption><p>Slack notification showing newly discovered issues with rustls-webpki in trunk-io/trunk</p></figcaption></figure>

</div>

## Learn more

[Read the documentation about our GitHub integration to learn more.](./GitHub%20Integration.md)

If you don't host your source code on GitHub, we recommend setting up Trunk locally.
