[< Models](develop_models.md)
# Models : Test the Server Side Model

SailsJS includes a [console](http://sailsjs.org/documentation/reference/command-line-interface/sails-console) feature for helping you test and debug your Server side models.

We are going to use this console to test out the example Model we created in [step 1a](develop_models_01_a_sailsManaged.md):  `ENRecipient`

```sh
# in your [sailsRoot]
$ sails console
```

This starts up an interactive console running within the sails environment.  

Let's try to create some new data for your `ENRecipient` model:
```sh
sails> ENRecipient.create([{ title:"X-Men", recipients:"profX@email.com, cyclopse@email.com" }, 
{ title:"Avengers", recipients:"capt@email.com, hulk@email.com"}]).then(console.log)
```
> NOTE: type that all on one line

When it executes, you should see a response like:
```sh
sails> [ { title: 'X-Men',
    recipients: 'profX@email.com, cyclopse@email.com',
    createdAt: Fri Mar 27 2015 13:43:11 GMT+0700 (ICT),
    updatedAt: Fri Mar 27 2015 13:43:11 GMT+0700 (ICT),
    id: 1 },
  { title: 'Avengers',
    recipients: 'capt@email.com, hulk@email.com',
    createdAt: Fri Mar 27 2015 13:43:11 GMT+0700 (ICT),
    updatedAt: Fri Mar 27 2015 13:43:11 GMT+0700 (ICT),
    id: 2 } ]
```

This tells us two instances of ENRecipients were created and their respective `id`, `createdAt`, and `updatedAt` fields were populated.

You can also practice searching for entries as well:
```sh
sails> ENRecipient.find({id:2}).then(console.log)
sails> [ { title: 'Avengers',
    recipients: 'capt@email.com, hulk@email.com',
    id: 2,
    createdAt: Fri Mar 27 2015 13:43:11 GMT+0700 (ICT),
    updatedAt: Fri Mar 27 2015 13:43:11 GMT+0700 (ICT) } ]
```

Verify that these values are properly setup in your MySQL table.

> NOTE:  The sails console will allow you to [tab] select available entries.  So typing `EN[tab]` would auto fill the known Model.  `ENRecipient.[tab]`  would display a list of available options.



[< Models](develop_models.md)     
Next: [test their REST interface >](develop_models_03_testBlueprints.md)