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

The OpsPortal is a Single Page Application Widget that is served up by a Sails server.  So in order to develop an OpsPortal Tool, we'll need to
  - setup Sails, 
  - install our OpsPortal plugin
  - create a web page to hold the widget

### Setup Sails
```sh
$ cd your/development/directory
$ appdev install sails
    # you will be asked numerous configuration questions.  
    # In many cases you can simply [enter] to accept the default 
    # options:
    #     mysql : setup according to typical MAMP settings
    #     SSL   : no
    #     auth  : local
```

### Install OpsPortal plugin
```sh
$ cd sails
$ npm install --save appdevdesigns/appdev-opsportal
```

*Note: if you would like to install the `#develop` version you can do:*
```sh
$ npm install --save appdevdesigns/appdev-opsportal#develop
```

In order to see the OpsPortal with some additional Tools installed, you can do:
```sh
$ npm install --save  appdevdesigns/opstool-hrisAdminObjects#develop
$ npm install --save  appdevdesigns/opstool-hrisUserProfile#develop
```

In a development environment, you will want to allow SailsJS to modify the DB tables based upon the changes you make in your models.  So set the `config/models.js` settings to:
```sh
$ vi config/models.js
   # change migrate:'safe'  to migrate:'alter'
```

The default OpsPortal config setup includes many of our existing OpsTools. You will need to edit the `config/opsportal.js` to remove those.
```sh
$ vi config/opsportal.js
   # comment out the uninstalled OpsTools
```

### Create a Web Page
Now the OpsPortal is a javascript widget that sits on a web page.  So we need to create a default web page to place our OpsPortal widget on:
```sh
$ appdev page opsportal
```

This default page has created a `views/page/opsportal.ejs` template and will be available to Sails with the url: `page/opsportal`

We need to edit this template to show our OpsPortal widget:
```sh
$ vi views/page/opsportal.ejs
```

Remove the final: 
```html
<script type='text/javascript'  src='../steal/steal.js?pages/opsportal/router.js' ></script>
```
and replace with:
```html
<div id='portal'></div>
<script type='text/javascript' src='../steal/steal<%- sails.config.environment == 'development'? '': '.production' %>.js?OpsPortal'></script>
```

Finally, we need to make sure that people attempting to load this page are
authenticated:
```sh
$ vi config/policies.js
```
Add this to the policy definition:
```javascript
PageController:{
    opsportal:['sessionAuth']
}
```


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

