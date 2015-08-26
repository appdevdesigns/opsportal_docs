[< Develop](Develop.md)
# Configure the OpsPortal

Just physically being in the directory doesn't tell the OpsPortal to include you.  We need to add it to the `config/opsportal.js` file.

```sh
$ cd ../..  
   # should be back to the [sails] root directory now
$ vi config/opsportal.js
```


We are going to add a new "Area" to the OpsPortal, and install this new Tool under that Area.  Add this to the config file:
```sh
      {
          // New Area for [pluginName]
          icon:'fa-cogs',
          key:'[pluginName]',
          label:'[pluginName]',
          tools:[{
              // New Tool entry for: [toolName]
              controller:'[toolName]',
              label:'[toolName]',
              isDefault: true,
              permissions:[
                  '[plugin].[tool]'
                  , 'adcore.developer'
              ]
          }]
      },
```

+ `[pluginName]` : is a string for your plugin
+ `[toolName]` : is a string Name for this tool
+ `[plugin].[tool]` : is a permissions string required to grant access to this tool


Now when the OpsPortal loads, you should see a `[pluginName]` area under the Menu, and a `[toolName]` tool that shows up when that is selected.

[< Develop](Develop.md)