---
description: Integrate with Trunk CI Analytics using the API
---

# API Reference

## REST API Overview

The Trunk CI Analytics API aims to give you access to instrument any CI or build system.

The API is an HTTP REST API.

The API uses status codes to indicate the success or failure of requests, returns JSON from all requests, and uses standard HTTP response codes.

Use the Trunk CI Analytics API to access Trunk CI Analytics programmatically.

The current version of the Trunk CI Analytics API is v1.

## Getting Started

Authenticate to the API with an API key using the header `x-api-token`.

### Finding your API token

You can find your API token in the [Trunk App](https://app.trunk.io).

## Endpoints

The track events endpoint uploads event data about a CI run.

Each event contains a start and end timestamp, metrics, tags, and an optional parent association ID.

application/json

Trunk API Token

The repository in which the CI Event ran.

See [repo](https://docs.trunk.io/ci-analytics/setup/api#repo) schema.

An array of event objects.

See [event](https://docs.trunk.io/ci-analytics/setup/api#event) schema.

Name of the uploading client

#### Repo

Repo object properties:

| Property | Description                                                                              | Required   |
| :------- | :--------------------------------------------------------------------------------------- | :--------- |
| `host`   | The name of the code host for this repository. Currently only `github.com` is supported. | `Required` |
| `owner`  | The owner of this repository                                                             | `Required` |
| `name`   | The name of this repository                                                              | `Required` |

For example, the repository `https://github.com/trunk-io/jenkins-plugin` would be

```json
{
  "host": "github.com",
  "owner": "trunk-io",
  "name": "jenkins-plugin"
}
```

#### Event

Event object properties

| Property     | Description                                                                                                                                           | Required   | Type     |
| :----------- | :---------------------------------------------------------------------------------------------------------------------------------------------------- | :--------- | :------- |
| `id`         | A unique identifier for this event                                                                                                                    | `Required` | `String` |
| `chainId`    | The root event id. `chainId` is used to group all events that were part of the same run.                                                              | `Required` | `String` |
| `parent`     | The parent event. Not required if this event has no parent.                                                                                           | `Optional` | `Object` |
| `createdAt`  | The millisecond epoch marking the start of this event                                                                                                 | `Required` | `Number` |
| `finishedAt` | The millisecond epoch marking the end of this event                                                                                                   | `Optional` | `Number` |
| `conclusion` | `"SUCCESS" \|FAILURE" \|"CANCELLED" \|"TIMED_OUT" \|"SKIPPED"`                                                                                        | `Optional` | `String` |
| `payload`    | An object containing metrics and tags. See [payload](https://docs.trunk.io/ci-analytics/setup/api#payload) schema.                                    | `Optional` | `Object` |
| `sequence`   | Describes the workflow, pipeline, or job, etc. that this event is from. See [sequence](https://docs.trunk.io/ci-analytics/setup/api#sequence) schema. | `Optional` | `Object` |

#### Parent

Parent object properties:

| Property      | Description                            | Required   | Type     |
| :------------ | :------------------------------------- | :--------- | :------- |
| `eventId`     | The parent event's unique identifier   | `Required` | `String` |
| `sequenceKey` | The key to the parent event's sequence | `Required` | `String` |

#### Payload

Payload object properties:

| Property  | Description                                                                                                                                             | Required   | Type     |
| :-------- | :------------------------------------------------------------------------------------------------------------------------------------------------------ | :--------- | :------- |
| `metrics` | Each metric is an object containing the metric key and value (where value is a number). For example: `{"k": "duration_ms", "v": 101920}`                | `Required` | `Object` |
| `tags`    | Each metric is an object containing the tag key and value (where value can be a number or a string). For example: `{"k": "runner", "v": "self-hosted"}` | `Required` | `Object` |

#### Sequence

A sequence describes the CI pipeline, job, workflow, etc.

Events are grouped under sequences using the `sequenceKey`.

Sequence object properties:

| Property   | Description                                                                                                                                                | Required   | Type     | Examples                     |
| :--------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------- | :------- | :--------------------------- |
| `platform` | The string name of the CI platform. Must be `snake_case`.                                                                                                  | `Required` | `String` | `jenkins`, `trunk_merge`.    |
| `kind`     | The type of the sequence.                                                                                                                                  | `Required` | `String` | `job`, `workflow`            |
| `key`      | An identifier for this `sequence`. Must be unique within the `repo` and < 128 characters.                                                                  | `Required` | `String` | `my-global-uuid`             |
| `name`     | The name of this `sequence`. These will appear as top-level entries in your CI Analytics dashboards.                                                       | `Required` | `String` | `Integration Tests [linux]`. |
| `payload`  | The tags for this sequence. See [payload](https://docs.trunk.io/ci-analytics/setup/api#payload) schema. (note: metrics are not supported for a `sequence`) | `Required` | `Object` | `{metrics: [],tags: []}`     |

#### Example Request

```shell
curl \
    -i \
    -X POST https://api.trunk.io/v1/metrics/trackEvents \
    -H "Content-Type: application/json" \
-H "x-source: curl-sample" \
    -H "x-api-token: {REDACTED}" \
    -d '{
    "repo": {
        "host": "github.com",
        "owner": "trunk-io",
        "name": "jenkins-plugin"
    },
    "events": [{
            "id": "fba555ee-90ce-42ca-a60c-38d200e9290e",
            "chainId": "b0ea8b0d-6574-4421-a63b-56f04eab9738",
            "createdAt": 1694616487000,
            "finishedAt": 1694615487000,
            "conclusion": "SUCCESS",
            "sequence": {
                "platform": "Jenkins",
                "kind": "pipeline",
                "key": "test-pipeline",
                "name": "Trying out a test pipeline",
                "payload": {
                    "tags": [{
                        "k": "label",
                        "v": "self-hosted"
                    }]
                }
            },
            "payload": {
                "metrics": [{
                        "k": "duration_ms",
                        "v": 1000
                    },
                    {
                        "k": "api_latency",
                        "v": 500
                    }
                ],
                "tags": [{
                        "k": "branch",
                        "v": "merge-PR-1"
                    },
                    {
                        "k": "sha",
                        "v": "88962a71bcfa92e5950670fc7e3f2b9f8d993b87"
                    }
                ]
            }
        },
        {
            "id": "test-workflow-a-1",
            "chainId": "test-workflow-a-1",
            "createdAt": 1694616487000,
            "finishedAt": 1694615487000,
            "conclusion": "SUCCESS",
            "parent": {
                "eventId": "fba555ee-90ce-42ca-a60c-38d200e9290e",
                "chainId": "b0ea8b0d-6574-4421-a63b-56f04eab9738"
            },
            "sequence": {
                "platform": "Jenkins",
                "kind": "nested-pipeline",
                "key": "test-pipeline/nested-pipeline",
                "name": "Trying out a test nested pipeline",
                "payload": {
                    "tags": [{
                        "k": "label",
                        "v": "self-hosted"
                    }]
                }
            },
            "payload": {
                "metrics": [{
                        "k": "duration_ms",
                        "v": 1000
                    },
                    {
                        "k": "api_latency",
                        "v": 500
                    }
                ],
                "tags": [{
                        "k": "branch",
                        "v": "merge-PR-1"
                    },
                    {
                        "k": "sha",
                        "v": "88962a71bcfa92e5950670fc7e3f2b9f8d993b87"
                    }
                ]
            }
        }
    ]
}'
```
