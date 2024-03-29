# Telemetry

Trunk Check usage data tracking.

Trunk sends basic usage metrics from our local tools ([CLI](https://docs.trunk.io/check/advanced-setup/cli) & [VS Code Extension](https://docs.trunk.io/check/ide-integration/vs-code)) to our analytics system to help us understand our usage and improve our tools over time.

We do not send your code or codebase to our backend.

## Why we collect usage data

Our product team constantly works on feature enhancement and new areas to invest in.

Usage data allows us best to understand the ergonomics and performance of our tools.

For example, if we add a new subcommand to the command line interface - how often is it used? Additionally, usage data is gathered to track usage and compliance against our free and paid product offerings.

To give concrete examples: we track our users' client version and operating system to understand backward compatibility requirements, and the time it takes our user base to upgrade to our latest releases.

## Example usage data

```json
{
  "anonymous_id": <GUID>,
  "command":  "check --all",
  "launcher_version": "1.2.3",
  "os": "macOS",
  "release": 1.4.1,
  "source": "client",
  "time": <UTC_TIMESTAMP>,
  "exit_code": 0,
  "duration_ms": 232,
  "repository":<REPO_IDENTIFIER>
}
```

## Can I disable usage data?

Yes.

You can disable usage telemetry by setting the following environment variable:

```shell
TRUNK_TELEMETRY=off
```
