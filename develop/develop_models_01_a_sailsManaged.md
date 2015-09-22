[< Models : Create the Server Side Model](develop_models_01_create.md) 
# Models : Let Sails Manage Your Tables

When booting up, SailsJS will check the model definitions and verify they accurately reflect the MySQL table definitions.  If they don't, Sails can alter the table definitions to match the Model definition.  This makes keeping the MySQL table and the Sails Model in sync very easy during development.  

If this is the route you want to take, then first give permission to Sails to alter the MySQL tables:
```sh
# in [sailsRoot]
$ vi config/models.js
// change the 'migrate' : 'safe'   to 'migrate':'alter'
```

Now we want to create a SailsJS model to represent the resource (DB Table)  we are working with.  
> note: SailsJS comes with a command line generator to create models and controllers:  `sails generate model [field1]:[type1] [field2]:[type2] .... [fieldN]:[typeN]`   our appdev tool reuses the Sails generator for those files, and then adds more functionality.  In our example here, we will use the appdev commands instead
```sh
# in your [plugin] root
$ appdev resource [application] [ModelName] [field1:type1] [field2:type2] ... [fieldN:typeN]
```
> `[application]` is the name of the client side folder for the client side Model
> `[ModelName]`  name of the model to create
> `[field:type]` a series of fields with their respective [data types](http://sailsjs.org/documentation/concepts/models-and-orm/attributes)

If you're developing an opstool then the `[application]` should match the directory path from the `assets/` folder.  
> if your opstool is called "ProcessApproval", then your `[application]` = "opstools/ProcessApproval"


So for example, if we are creating a Recipient Model for an EmailNotification opsportal application with two fields:
```sh
$ appdev resource opstools/EmailNotification ENRecipient title:string recipients:text
```
>Note: here we namespace the model with "EN" and keep the Model singular:  ENRecipient

This command creates:
```sh
# from the [pluginRoot]
api/models/ENRecipient.js                             // the SailsJS Model definition 
api/controllers/ENRecipientController.js              // when SailsJS has a controller the same name as a Model, it creates a RESTful web interface for it
assets/[application]/models/ENRecipient.js            // the CanJS client side Model definition
assets/[application]/models/base/ENRecipient.js       // the CanJS client side Model definition with the connection info set
       # [application]  = opstools/EmailNotification
```



By default, Sails will create a table with the same name as the model (`enrecipient`).  But you can specify the table name by:
```sh
$ vi api/models/ENRecipient.js
```

And add the `tableName` attribute:
```javascript
module.exports = {

  connection:"appdev_default",

  tableName:"en_recipient",		// <-- namespace this table with 'en_' 

  attributes: {

    title : { type: 'string' },

    receipients : { type: 'text' }
  }
};

```

>Pay attention:  you have added files to your plugin, but Sails wont recognize them until you do:
```sh
$ node setup/setup.js
```

Once you have done that, you can start Sails:
```sh
# in your [sailsRoot]
$ sails lift
```

After sails starts, you can now check your DB and see that your new table created. If you explore the MySQL definition of the table, you will see it looks like:
```sql
CREATE TABLE `en_recipient` (
  `title` varchar(255) DEFAULT NULL,
  `recipients` longtext,
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `createdAt` datetime DEFAULT NULL,
  `updatedAt` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8;

```

>Notice: sails automatically adds fields:  `id`, `createdAt`, `updatedAt`.  Unless you have a good reason not to, stick with the sails generated defaults.



[< Models : Create the Server Side Model](develop_models_01_create.md)     
Next: [connect to an existing table and have Sails access them >](develop_models_01_b_existing.md)