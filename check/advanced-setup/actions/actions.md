# Actions

You can think of Trunk Actions as IFTTT for your repository. An action is a command that is run when a specified trigger occurs.

The most common Trunk Actions are provided out of the box with trunk, and are triggered to invisibly autoformat (`trunk fmt`) your commits every time you `git commit`, and run `trunk check` when you `git push`.

### Triggers

There are several different types of Trunk Actions, based on when they are triggered:

| Trigger                                                                  | Description                                                          |
| :----------------------------------------------------------------------- | :------------------------------------------------------------------- |
| time-based                                                               | run on a schedule (once per hour, once per day, once per week)       |
| file modification                                                        | run whenever a file or directory in your repo changes.               |
| [githooks](https://docs.trunk.io/check/advanced-setup/actions/git-hooks) | run whenever a listed githook event fires (e.g. pre-commit, on-push) |
| manual                                                                   | `trunk run <action-name>`                                            |

### **Command Line**

| trunk actions <command> | Description                                                          |
| :---------------------- | :------------------------------------------------------------------- |
| `list`                  | list all available actions in the repository                         |
| `history <action-name>` | print the history for execution of the provided action               |
| `enable <action-name>`  | enable the provided action                                           |
| `disable <action-name>` | disable the provided action                                          |
| `run <action-name>`     | manually trigger the provided actionalias: `trunk run <action-name>` |

### Discovering Actions

The trunk [plugins](https://github.com/trunk-io/plugins) repo ships with a collection of actions that can help supercharge your repository and provide examples of how to write your own actions.

To see a list of actions that you can enable in your repo run:

```shell
trunk actions list
```

![ ](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2Fgit-blob-dbd8b50c0181d1cd831e77f778eed931229bb0ad%2Fimage.png?alt=media)
_List of actions reported by `trunk actions list`_

### Enable/Disable Actions

Trunk only runs actions listed in the `enabled` section of your `trunk.yaml`.

Some built-in actions are enabled by default and can be disabled explicitly by adding them to the disabled list.

You can always run `trunk actions list` to check the enabled status of an action.

```yaml
actions:
  enabled:
    - trunk-announce
    - git-lfs
    - trunk-check-pre-push
    - trunk-fmt-pre-commit
    - trunk-cache-prune
    - trunk-upgrade-available
```
