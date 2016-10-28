[< Develop](Develop.md)
# Setup Your Development Environment

In order to develop for the OpsPortal, you need to install the following packages:

>Note: For an easier setup on Windows try using Docker.  You can try installing our tools using Docker by going [here](develop_setup_docker.md).

  - [Git](https://git-scm.com/downloads)
  - [Mysql](https://www.mamp.info/en/downloads/) (optional: only if you want to use mysql)
  - [NodeJS](http://nodejs.org/download/) (v6.x)
  - [SailsJS](http://sailsjs.org/#/) (currently supporting v0.12.3)
  - [appdev-cli](https://github.com/appdevdesigns/appdev-cli) (recommended)
>Note: Windows users will also need [python v2.x](https://www.python.org/downloads/). 


First, make sure you download and install [Git](https://git-scm.com/downloads) and [Mysql](https://www.mamp.info/en/downloads/).  Then download the appropriate [NodeJS](http://nodejs.org/download/) package and install it.  Once that is done, you will also have a command line tool called `npm`

The rest of the setup can be done from the command line:
```sh
$ npm install -g sails@0.12.3
$ npm install -g appdevdesigns/appdev-cli
```
>Note: Windows users will need to run their command line tool in Administrator mode.  After you install Git, you should have access to **'Git Bash'**.  Right click on the icon (probably on your desktop), and select "run as administrator".

Now we simply need to run the appdev install tool:
>Note: if you are using Mysql you will be asked for a Database to use.  **Make sure you have already created that DB before running this install command.**

```sh
$ cd your/development/directory
$ appdev install sails --develop
    # you will be asked numerous configuration questions.  
    # In many cases you can simply [enter] to accept the default 
    # options that are displayed in the ():
    #     mysql : setup according to typical MAMP settings
    #     SSL   : no
    #     auth  : local
```

Alternatively, if you are the kind of person who likes seeing how everything is done under the hood, then you can leave off the `--develop` parameter, and step through the process manually.  Read how to do that [here](develop_setup_manual.md).


#### Test it out
Now test this out:
```sh
$ cd sails
$ sails lift
```


You should see a response like:
```sh

info: Starting app...

info: 
info: 
info:    Sails              <|
info:    v0.12.3             |\
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

Your first time to load this page, you will be presented with a Login Screen.  The default login info is:

- username: **admin**
- password: **admin**


After you login, you should see a spiffy web page with a bar across the bottom for the Opsportal.  Clicking on the bar will cause the OpsPortal to take over the whole screen.  Clicking on the menu icon will allow you to switch between the installed OpsTools.

If you successfully got here, then you are ready to create your first OpsTool Plugin.


[< Develop](Develop.md)     
Next: [Create a Plugin >](develop_plugin_create.md)