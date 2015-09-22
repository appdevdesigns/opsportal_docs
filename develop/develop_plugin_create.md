[< Develop](Develop.md)
# Create An OpsTool Plugin

We package our applications into smaller npm modules that can be installed into a Sails installation.  In order to make this process easier, we created an appdev command to help create your initial npm package:
```sh
$ cd node_modules/
$ appdev plugin [pluginName]
   # [pluginName] is whatever you want to name your new plugin
   # there will be a series of questions about your npm package:
   #    client side unit tests:  for testing out UI
   #    author / description / version : yeah, you can figure those out
   #    git repository:  by default appdevdesigns/[pluginName] <-- you'll wanna change that to yours.

```

#### Naming Conventions
In order to keep your new plugin from interfeering with other plugins, we suggest you name your plugin in the format: [application]-[tool]  (or  opstool-[application]-[tool] )

So if you are developing a financial reimbursement tool then:  finance-reimbursement ( opstool-finance-reimbursement )
Or if you are developing an MPD report tool : mpd-13MonthReport ( opstool-mpd-13MonthReport )


## Test it out
Once the command completes you'll have an empty AppDev Plugin directory at [sails]/node_modules/[pluginName].

Now check to see the unit tests have been properly setup:
```sh
$ cd [pluginName]
$ npm test
```

You should see something like:
```sh
> [pluginName]@0.0.0 test /path/to/your/sails/node_modules/[pluginName]
> make test


  0 passing (1ms)
```

Which is good, since we haven't actually written any unit tests yet.


##Configure the OpsPortal
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
              // New Tool entry for: [pluginName]
              controller:'FCFActivities',
              label:'Dashboard',
              isDefault: true,
              permissions:[
                  'fcf.activities'
                  , 'adcore.developer'
              ]
          }]
      },
```

## Run the setup routine
At this point, we have an NPM module in your sails' /node_modules directory.  But sails doesn't know about the resources provided by your plugin.  So we have a setup routine that will make sure your plugin's resources are available to the enclosing sails application:

```sh
# from your [plugin] directory
$ node setup/setup.js
```

You should see a list of activity similar to this:
```
    setting up module: [pluginName]
    
    Making Directories:
    
    Copying Files:
    
    Creating Symbolic Links:
    exists:   /api/controllers/[pluginName]
    exists:   /views/[pluginName]
    
    Linking Individual Files:
    exists:   /assets/opstools
    exists:   /assets/opstools/ProcessApproval (ours) 
    
    Patching Sails files:
    SKIP:     already patched: /config/routes.js
    SKIP:     already patched: /config/policies.js
    SKIP:     already patched: /config/connections.js
    SKIP:     already patched: /config/bootstrap.js
    
    Patching config/local.js:
    
    Patching .gitignore:
    
    Patching assets/stealconfig.js
    exists: shim: { "site/labels/opstools-ProcessApproval.js":[object Object] }  same
    
    setup finished

```


It is important to note that everytime you add a new Model, or Controller to your plugin, you will need to rerun this command.  If you don't Sails will not be able to see it.


## What you have
After running this command, you have an NPM package that defines a plugin to your Sails application.  

The NPM package file structure is modeled after the Sails application's [file structure](http://www.sailsjs.org/documentation/anatomy/my-app).

We provide a few additional directories:

+ `/setup`              :  scripts to help with the install of a plugin
+ `/setup/labels`       :  multilingual label and content definition for your plugin
+ `/setup/permissions`  :  plugin permission definitions
+ `/tests`              :  unit tests for your server side resources





[< Develop](Develop.md)
[create a client side OpsPortal Tool >](develop_client_opstool.md)
