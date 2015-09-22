[< Models : Create the Server Side Model](develop_models_01_create.md) 
# Models : Working with an Existing Table

Sometimes you are having to connect to an existing system where the MySQL DB is already created for you.  In this case, we will generate a Sails model and make sure Sails doesn't add any of it's default fields.

>Note: for this example, I'm just going to run this command on the existing table that I created in [step 1a](develop_models_01_a_sailsManaged.md).  I'll name the new Model `ENRecipient2`, so you can see the difference in what is generated.

Assuming you want to generate a model from an existing table `en_recipient` for your `EmailNotifications` opsportal tool, run the following command:
```sh
# from your plugin directory
$ appdev table2model opstools/EmailNotifications en_recipient ENRecipient2
```
>Note: this will use the default connection as defined in `[sails]/config/models.js`  If you want to access a DB table from another connection setting, you can add `connection:[name]` to the command.

> if you need more info about the table2model command, you can always run the `$ appdev help table2model` command

This command creates:
```sh
# from the [pluginRoot]
api/models/ENRecipient2.js                             // the SailsJS Model definition 
api/controllers/ENRecipient2Controller.js              // when SailsJS has a controller the same name as a Model, it creates a RESTful web interface for it
assets/[application]/models/ENRecipient2.js            // the CanJS client side Model definition
assets/[application]/models/base/ENRecipient2.js       // the CanJS client side Model definition with the connection info set
```


Now take a look at the sails model definition:
```javascript
// in api/models/ENRecipient2.js
module.exports = {

  tableName:"en_recipient",
  autoCreatedAt:false,
  autoUpdatedAt:false,
  autoPK:false,
  migrate:'safe',  // don't update this table

  connection:"appdev_default",

  attributes: {

    title : {
        type : "string",
        size : 255
    }, 

    recipients : {
        type : "text"
    }, 

    id : {
        type : "integer",
        size : 10,
        primaryKey : true,
        autoIncrement : true
    }, 

    createdAt : {
        type : "datetime"
    }, 

    updatedAt : {
        type : "datetime"
    }

  }
};

```

Since we are asking Sails to access an table we are managing outside of Sails, then we specify we don't want sails to auto create it's default fields: `id`, `createdAt`, `updatedAt`:
```javascript
  autoCreatedAt:false,
  autoUpdatedAt:false,
  autoPK:false,
```
> NOTE: this also means that another field in your table needs to be marked :  `primaryKey: true,`

We also don't want Sails to manually mess with the table, no matter what the `[sails]/config/models.js` setting is set to:
```javascript
  migrate:'safe',  // don't update this table
```

>NOTE: because we originally created this table using sails in [step 1a](develop_models_01_a_sailsManaged.md), it already has the `id`, `createdAt`, and `updatedAt` fields defined.

Finally, remember that these files are created in your plugin directory, but sails wont recognize them until you do:
```sh
# in your [pluginRoot]
$ node setup/setup.js
```



[< Models : Create the Server Side Model](develop_models_01_create.md)     
Next: [test the server side model >](develop_models_02_testServer.md)