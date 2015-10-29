[< Tutorial Sprint 1](tutorial_sprint1.md)
# Tutorial - Limit access to the find() operation to users with the proper permission

### Overview
Continue securing our external API, this time implementing [permissions](../develop/develop_permissions.md).


### Define our Action Permission Key
In our `opstool-process-approval` tool, we are going to define the following actions:

| Action Key |  description |
|------------|--------------|
| process.approval.tool.view | Gives the user the ability to access the Process Approval Tool |


Open up our `[plugin]/setup/permissions/actions.js` file and add a definition:
```javascript
module.exports = {

    language_code:'en',

    actions:[
        { 
            action_key:'process.approval.tool.view', 
            action_description:'Gives the user the ability to access the Process Approval Tool.' 
        }
    ]

};
```


Now restart Sails and you should see a new `creating: permission action:`  message:
```sh
$ sails lift

info: Starting app...

    route: /appdev-core registered
    route: /opsportal/requirements registered
    route: /opsportal/config registered
    creating: permission action:process.approval.tool.view

... 
```


### Require this key when requesting access to our PARequest API:

Now we need to tell our framework to look for this new action key when anyone is requesting access to our PARequest controller.

Open your `[pluging]/setup/permissions/routes.js` file and add a definition:
```javascript
module.exports = {

    // this will work for 'get' and 'post':
    '/opstool-process-approval/parequest' : [ 'process.approval.tool.view' ]

};
```

Restart Sails and you will see a new notification of this route being loaded:
```sh
$ sails lift

info: Starting app...


    route: /appdev-core registered
    route: /opsportal/requirements registered
    route: /opsportal/config registered
    route: /opstool-process-approval/parequest registered
...
```


Now go back to your browser and try to access: `http://localhost:1337/opstool-process-approval/parequest`  

You should now see a nifty **forbidden** page.


Also, the sails console should have printed an error:
```sh
debug: --------------------------------------------------------
debug: :: Wed Oct 07 2015 17:16:04 GMT+0700 (ICT)

debug: Environment : development
debug: Port        : 1337
debug: --------------------------------------------------------
    .... reqPath[get /opstool-process-approval/parequest]  -> user did not have any of the required permissions process.approval.tool.view

```


So now it looks like our Permissions are working.  But for us to continue to develop the program, we need to give our current user account (`admin`) permission to `process.approval.tool.view`



### Adding Permission for the admin user to view the page.

In order to allow you to access that resource again, we now need to make sure your user account (`admin`) is assigned to the permission we just created (`process.approval.tool.view`).  To do that, we edit one of the Roles that is assigned to you and then add this action to that role:

+ open up the opsportal in your web browser
+ click [menu] and then the [administration] tab
+ now click on the [roles] menu option and select one of your Roles (`Sys)

///// LEFT OFF HERE:  show the admin assignments portal and assigning permissions.


---
[< step 5 : Lockdown unused interfaces](tutorial_sprint1_05_lockdownAPI.md) 
[step 7 : .... >](tutorial_sprint1_07_????.md) 