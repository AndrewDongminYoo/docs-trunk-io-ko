---
description: Automate your code quality enforcement with just a few clicks.
---

# GitHub Integration

Now that you have `trunk check` running on your local computer, your next step is to run Trunk automatically in the cloud and share notifications with your whole team. _Start by connecting your Trunk organization to GitHub._

## Connect your Trunk organization to GitHub

Sign up at [app.trunk.io](https://app.trunk.io), create a Trunk organization, and connect it to your repositories. See our GitHub App permissions [here](../../../administration/github-app-permissions.md) if interested.

![ ](https://d2vsad3r6ug0tf.cloudfront.net/cllobexas0hc0051asw4lmlyr/eIV7VryvgLhT9Qla3y.png)

## Set Up Trunk Check

![ ](https://d2vsad3r6ug0tf.cloudfront.net/cllobexas0hc0051asw4lmlyr/KTgGNj--kvEhDKtxts.png)

## Configure Slack Notifications (optional)

![ ](https://d2vsad3r6ug0tf.cloudfront.net/cllobexas0hc0051asw4lmlyr/B8syrVqgaXK0BUTF5y.png)

## Use it

### Ensure that PRs are free of issues

Check out [this example](https://github.com/trunk-io/plugins/pull/424/checks?check_run_id=15730277425) in our `plugins` repository!

<div data-full-width="true">

<figure><img src="https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2FsAz3xEetNR7hwQmi9YDb%2Fimage.png?alt=media&token=a34f5b1c-d9c5-4551-a09f-38d48d64388c" alt=""><figcaption><p>Trunk Check identifying a security issue in one of Trunk's own repositories</p></figcaption></figure>

</div>

### Explore issues in your repository

<figure><img src="https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2FUFUcUsBoLuaeRBwu11lu%2FScreenshot%202023-08-23%20173119.png?alt=media&token=a847e816-b8a5-4f12-96e3-211f50226170" alt=""><figcaption><p>Trunk Check showing all the issues present in trunk-demo1/sass</p></figcaption></figure>

### Get Slack notifications about new issues in your repository

Not only do our daily scans allow you to browse and triage the issues in your repository, but they can also notify you when new security issues are discovered in packages you already depend on.

<div data-full-width="true">

<figure><img src="https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2FbvI4fHl70HWiGaIguSzz%2FScreenshot%202023-08-23%20173252.png?alt=media&token=730e34ba-9bb8-4fd2-aeb8-d7f56d52af79" alt=""><figcaption><p>Slack notification showing newly discovered issues with rustls-webpki in trunk-io/trunk</p></figcaption></figure>

</div>

## Learn more

[Read the documentation about our GitHub integration to learn more.](github-integration.md)

If you don't host your source code on GitHub, we recommend setting [up Trunk locally](../../usage.md#ci-setup).
