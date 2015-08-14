[< Develop](Develop.md)
# Setup Your Development Environment

In order to develop for the OpsPortal, you need to install the following packages:

  - [NodeJS](http://nodejs.org/download/)
  - [SailsJS](http://sailsjs.org/#/)
  - [appdev-cli](https://github.com/appdevdesigns/appdev-cli) (recommended)

First, make sure you download the appropriate [NodeJS](http://nodejs.org/download/) package and install it.  When that is done, you will also have a command line tool called `npm`

The rest of the setup can be done from the command line:
```sh
$ npm install -g sails
$ npm install -g appdevdesigns/appdev-cli
```

Now we simply need to run the appdev install tool:
```sh
$ cd your/development/directory
$ appdev install sails --develop
    # you will be asked numerous configuration questions.  
    # In many cases you can simply [enter] to accept the default 
    # options:
    #     mysql : setup according to typical MAMP settings
    #     SSL   : no
    #     auth  : local
```

Alternatively, if you are the kind of person who likes seeing how everything is done under the hood, then you can leave off the `--develop` parameter, and step through the process manually.  Read how to do that [here](develop_setup_manual.md).


#### Test it out
Now test this out:
```sh
$ sails lift
```


You should see a response like:
```sh

info: Starting app...

info: 
info: 
info:    Sails              <|
info:    v0.10.5             |\
info:                       /|.\
info:                      / || \
info:                    ,'  |'  \
info:                 .-'.-==|/_--'
info:                 `--'-------' 
info:    __---___--___---___--___---___--___
info:  ____---___--___---___--___---___--___-__
info: 
info: Server lifted in `/path/to/your/dir/sails`
info: To see your app, visit http://localhost:1337
info: To shut down Sails, press <CTRL> + C at any time.

debug: --------------------------------------------------------
debug: :: Fri Feb 13 2015 17:24:55 GMT+0700 (ICT)

debug: Environment : development
debug: Port        : 1337
debug: --------------------------------------------------------
```


Open a web browser and enter the path: `localhost:1337/page/opsportal`

You should see a spiffy web page with a bar across the bottom for the Opsportal.  Clicking on the bar will cause the OpsPortal to take over the whole screen.  Clicking on the menu icon will allow you to switch between the installed OpsTools.

If you successfully got here, then you are ready to create your first OpsTool Plugin.


[< Develop](Develop.md)     
Next: [Create a Plugin >](develop_plugin_create.md)