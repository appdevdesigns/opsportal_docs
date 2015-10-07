[< Models](develop_models.md)
# Models : Test their REST interface

In SailsJS, if you define a Model and a Controller with the same Model name, then Sails will automatically setup a RESTful interface for your model using [blueprints](http://sailsjs.org/documentation/reference/blueprint-api).

Our appdev tools:  `appdev resource ...`  and `appdev table2model ...`  create both a server side model instance, and a corresponding controller for that model.  When sails loads, your new model should have a RESTful interface running.

For this example lets assume that

+ you [created a new plugin](develop_plugin_opstool.md) named `opstool-emailNotification` :  `appdev opstoolplugin opstool-emailNotification`
+ you [created a new model](develop_models_01_a_sailsManaged.md) named `ENRecipient` : `appdev resource opstools/EmailNotification ENRecipient title:string recipients:text`


Now start sails:
```sh
# in your [sailsRoot]
$ sails lift
```

Open up a web browser, and put this in for the url: `http://localhost:1337/opstool-emailNotification/enrecipient`
> NOTE: the default format for accessing your blueprint models is : `get /:modelIdentity` or `get /:modelIdentity/:id`
>
> However, in our appdev plugin format, we need to prefix the model with the plugin name: `get /:pluginName/:modelIdentity`
> 
> so in this case, our plugin was `opstool-emailNotification` so our url is: `/opstool-emailNotification/:modelIdentity`

You should see the following response in your browser:
```javascript
[
  {
    "title": "X-Men",
    "recipients": "profX@email.com, cyclopse@email.com",
    "id": 1,
    "createdAt": "2015-03-27T06:43:11.000Z",
    "updatedAt": "2015-03-27T06:43:11.000Z"
  },
  {
    "title": "Avengers",
    "recipients": "capt@email.com, hulk@email.com",
    "id": 2,
    "createdAt": "2015-03-27T06:43:11.000Z",
    "updatedAt": "2015-03-27T06:43:11.000Z"
  }
]
```




[< Test the Server Side Model](develop_models_02_testServer.md)    
Next: [update the Client side Model >](develop_models_04_clientModels.md)