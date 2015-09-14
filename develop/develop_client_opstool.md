[< Develop](Develop.md)
# Create a Client Side Ops Tool

A single plugin can contain one or more client side OpsPortal tools.  These are the visual interface to your application that fit into the OpsPortal.

To do this, run the following command from your new plugin directory:
```sh
# from plugin directory
$ appdev opstool [toolName]
   # [toolName] is whatever you want to name your new tool interface
```

After this command is run, you should have a `[pluginDir]/assets/opstools/[toolName]` directory.


#### Naming Conventions
In order to keep your new plugin from interfeering with other plugins, we suggest you name your tool in the format: `[Application][Tool]` 

So if you are developing a financial reimbursement tool then:  `FinanceReimbursement` 
Or if you are developing an MPD report tool : `Mpd13MonthReport`


#### Rerun the setup command
Remember, every time you add a resource to your plugin, you will need to run the setup command for sails to know about it:
```sh
$ node setup/setup.js
```


## What you have
After running this command, your plugin should now contain a client side Ops Tool defined at: `[pluginDir]/assets/opstools/[toolName]`  

The directory structure contains:

+ `build.appdev.js`     :  specific minification and concatenation instructions for this tool
+ `build.config.js`     :  
+ `/classes`            :  any reusable UI classes
+ `/controllers`        :  contains the UI controllers 
+ `/controllers/[ToolName].js` : the master controller for this Tool. 
+ `/models`             :  contains the UI Model objects
+ `[ToolName].css`      :  any tool specific UI .css definitions for this tool
+ `[ToolName].js`       :  the initial entry point for the tool (defines dependencies)
+ `/tests`              :  client side unit tests for our tool
+ `/views`              :  any UI views for this tool
+ `/views/[ToolName]`   :  views are subdivided by the controller they belong to





[< Develop](Develop.md)
[configure a new OpsTool in the OpsPortal >](develop_opsportal_config.md)
