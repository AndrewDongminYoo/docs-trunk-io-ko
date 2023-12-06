#### 

Command Line[](https://docs.trunk.io/check/tools#command-line)

| 
trunk tools <command>



 | 

description



 |
| --- | --- |
| 

list



 | 

list all available tools in the repository and whether they are enabled or not



 |
| 

install



 | 

install your enabled tools into `.trunk/tools`



 |
| 

enable `<tool-name>[@version]`



 | 

enable the provided tool, optionally at a specified version



 |
| 

disable `<tool-name>`



 | 

disable the provided tool



 |

#### 

Discovering Tools[](https://docs.trunk.io/check/tools#discovering-tools)

The trunk [plugins](https://github.com/trunk-io/plugins) repo ships with a collection of tools that can help supercharge your repository and provide examples for how to write your own. To see a list of tools that you can enable in your own repo run:

![](https://682515401-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F61Ep9MrYBkJa0Yq3zS1s%2Fuploads%2FhFdViAxJM85qdbwlPwxk%2Fimage.png?alt=media&token=1d8fdf6c-1e2e-48ee-b53a-46a27f138d9e)

list of available and enabled tools

#### 

Running Tools[](https://docs.trunk.io/check/tools#running-tools)

`Trunk` installs your enabled tools into the `.trunk/tools` directory. Each tool exposes as list of **shims** (these may or may not be identically named to the tool - most typically a tool has one shim matching the name of the tool). Each shim is installed into the `.trunk/tools` directory.

You can run your tools by referring to the path `<path to your workspace>/.trunk/tools/<shim name>` but this is unwieldy. We highly recommend using our shell hooks to manage your PATH.

Starting with version 1.18.0, you can do so by running `trunk shellhooks install`, which will install the trunk hooks to the config file of your $SHELL. You can also run `trunk shellhooks install <shell_name>` to install a specific shell hook.

For organization that want to require the use of the hooks, they can add to the config file:

On the next trunk command (like check or fmt), it will update your shell RC file to load our hooks.

After reloading your shell, whenever you're inside your repo at the command line, you can just run shims installed by `trunk tools` directly by name.

N.B. There is a known incompatibility with direnv when using PATH\_ADD. To use our hooks, remove PATH\_ADD from your .envrc and add them to your trunk config as such:

Paths can either be absolute, or relative to the workspace using the special `${workspace}` variable