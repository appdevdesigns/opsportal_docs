[< Tutorial Sprint 1](tutorial_sprint1.md)
# Tutorial - Lockdown unused interfaces

### Overview
Now we have a feature branch for our server side updates.  Let's get to work tidying things up.


### Let's Go
To begin with, Sails offers some really handy [Blueprint APIs](http://sailsjs.org/documentation/reference/blueprint-api) to expose the data in your framework.  The blueprints offer several types of apis:  `rest`, `shortcut`, `actions`.

Our application will be exposing our PARequest resource using a REST API.  So that blueprint is going to be very handy for us in our project.


##### Shortcuts
The `shortcut` api is useful for development, in that it exposes the resource operations (find, create, update, destroy) in a way that you can easily type in a uri in a web browser and interact with the data.  For testing purposes, this is really handy otherwise you need to use another tool like [Postman](https://www.getpostman.com) to interact with your REST api.

But in our application we don't want to leave the `shortcut` api active.  So let's close this down:
```javascript
// your api/controllers/PARequestController.js

var fs = require('fs');
var path = require('path');
var fixtureData = null;


module.exports = {

    _config: {
        model: "parequest", 
        actions: true,
        shortcuts: false,   // <---- turn these off.
        rest: true
    },
```


##### Actions
The Actions blueprint will expose a route for each of the actions found on your Controller.  So if we added a PARequestController.flush()  method, then the actions blueprint would expose a `get /opstool-process-approval/parequest/flush` route that you can call as well.  

I'm not planning on creating any unique Actions on my PARequestController so let's turn that off:
```javascript
// your api/controllers/PARequestController.js

var fs = require('fs');
var path = require('path');
var fixtureData = null;


module.exports = {

    _config: {
        model: "parequest", 
        actions: false,		// <---- now turn these off.
        shortcuts: false,   
        rest: true
    },
```



##### create, destroy  methods
Now, in addition to the Blueprint APIs, we need to also close down our `create` and `destroy` methods.

According to our design document, our external interface should only be allowed to `find` a list of pending approvals, and `update` an approval's status.  The external client application is not supposed to be able to `create` or `destroy` an approval entry.  So, let's close those interfaces down:
```javascript
// your api/controllers/PARequestController.js
module.exports = {

    _config: {
        model: "parequest", // all lowercase model name
        actions: false,
        shortcuts: false,
        rest: true
    },


    find: function(req, res) {


    	if (fixtureData == null){
    		var data = fs.readFileSync(path.join(__dirname, '..', '..', 'test', 'fixtures', 'PARequest.json'));
    		fixtureData = JSON.parse(data);

    		fixtureData.splice(fixtureData.length-1, 1);  // <-- this one isn't supposed to be returned.

    	}
    	res.send(fixtureData);

    },

    create:function(req, res) {
    	// this is not allowed:
        res.forbidden();
    },

    destroy:function(req, res) {
    	// this is not allowed
    	res.forbidden();
    }

};
```


So, wanna test those out without having to use another app to submit the REST api calls?  Yeah, me too.  Change the shortcuts back on:
```javascript
// your api/controllers/PARequestController.js
module.exports = {

    _config: {
        model: "parequest", // all lowercase model name
        actions: false,
        shortcuts: true,    // <------ enable this
        rest: true
    },
```

Now restart sails:
```sh
# sails root directory
$ sails lift
```

now open a web browser and type in the following url:  `localhost:1337/opstool-process-approval/parequest/find`.  You should see our list of fixtures we sent back.  So the `find` method is working.

now enter:  `localhost:1337/opstool-process-approval/parequest/create`
You should see a **forbidden** message in your browser.

same for:  `localhost:1337/opstool-process-approval/parequest/destroy`


Now go back and disable the shortcuts:
```javascript
// your api/controllers/PARequestController.js
module.exports = {

    _config: {
        model: "parequest", // all lowercase model name
        actions: false,
        shortcuts: false,    // <------ disable this
        rest: true
    },
```


OK, save these changes in your new feature branch:
```sh
# from your [plugin] root:
$ git add .
$ git commit -m '+ADD: locked down unused PARequest APIs'
```



---
[< step 4 : Create our Server Side Feature branch](tutorial_sprint1_04_serverBranch.md) 
[step 6 : limit access to the find() operation >](tutorial_sprint1_06_restrictAccess.md) 