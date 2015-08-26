[< Develop](Develop.md)
# Create An OpsTool Plugin

We have a tool that steps through the process of:

+ [create a plugin](develop_plugin_create.md)
+ [create a client side OpsPortal Tool](develop_client_opstool.md)
+ [configure a new OpsTool in the OpsPortal](develop_opsportal_config.md)

It's a single command that gets the OpsPortal developers right where they want to go.

```sh
$ cd node_modules/
$ appdev opstoolplugin [pluginName]
   # [pluginName] is whatever you want to name your new plugin
   # there will be a series of questions about your npm package:
   #    client side unit tests:  for testing out UI
   #    author / description / version : yeah, you can figure those out
   #    git repository:  by default appdevdesigns/[pluginName] <-- you'll wanna change that to yours.

```

[< Develop](Develop.md)
[Tutorial Sprint 0 >](../tutorial/tutorial_sprint0_01_createPlugin.md)
