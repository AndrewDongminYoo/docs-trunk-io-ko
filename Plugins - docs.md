Plugins repositories allow users to expand the core capabilities of the Trunk CLI and to share configuration between different repositories.

#### 

Importing a plugin repository[](https://docs.trunk.io/check/plugins#importing-a-plugin-repository)

By default trunk imports the trunk-io/plugins repository. To import a repo add it to the `plugins` list. Each repo requires a URI and ref.

uri: https://github.com/trunk\-io/plugins

| 
field



 | 

description



 |
| --- | --- |
| 

id



 | 

unique identifier for this repository



 |
| 

uri



 | 

address used to clone the target repository



 |
| 

ref



 | 

commit id or tag to checkout



 |
| 

local



 | 

path to local (on-disk) repository. Takes precedence over uri/ref if defined



 |
| 

import\_to\_global (default: true)



 | 

import content into the global namespace. If set to false actions and linters defined in the plugin must be referenced by {plugin\_id}.{name}



 |

#### 

Plugin capabilities[](https://docs.trunk.io/check/plugins#plugin-capabilities)

Any configuration used in `trunk.yaml` can also be used in a plugin repository, with [some exceptions](https://docs.trunk.io/check/plugins#excluded-fields). A plugin repository must have one root level `plugin.yaml` and can have any number of other `plugin.yaml` files in other subdirectories. These configuration files are then merged into one composite plugin configuration.

The most common use for a plugin repository is to define custom linters, actions, or tools. But they can also be used to define a common set of shared tools across an organization. For more info, see [organization configs](https://docs.trunk.io/check/plugins/external-repositories).

The root `plugin.yaml` file may also have a `required_trunk_version` field which governs compatibility when [upgrading](https://docs.trunk.io/check/cli/upgrade) between CLI versions.

#### 

Add a plugin to your `trunk.yaml` file[](https://docs.trunk.io/check/plugins#add-a-plugin-to-your-trunk.yaml-file)

To add a plugin from GitHub:

trunk plugins add https://github.com/trunk-io/plugins --id=trunk

To add a plugin from GitHub at a specific version:

trunk plugins add https://github.com/trunk-io/plugins v1.2.6 --id=trunk

To add a plugin from a local repository:

trunk plugins add /home/user/self/hello-world --id=hello-world

#### 

Plugins scope[](https://docs.trunk.io/check/plugins#plugins-scope)

Plugins are merged serially, in the order that they are sourced, and can override almost any Trunk configuration. This allows organizations to provide a set of overrides and definitions in one central place.

For instance, you can create your own `my-plugins` repository with `plugin.yaml`:

\# override trufflehog to use '--only-verified'

run: trufflehog filesystem \--json \--fail \--only\-verified ${target}

sourced in a `.trunk/trunk.yaml` file from another repository as follows:

uri: https://github.com/trunk\-io/plugins

When a user runs `trunk` in the sourcing repository, they will already have `ruff` enabled, along with the `trufflehog` override from the `my-plugins` repository.

Note that private GitHub plugin repositories are not currently supported.

#### 

Excluded fields[](https://docs.trunk.io/check/plugins#excluded-fields)

Plugin `sources`, as well as the `cli` `version`, are not merged from plugin repositories to ensure that config merging occurs in a predictable, stable fashion.