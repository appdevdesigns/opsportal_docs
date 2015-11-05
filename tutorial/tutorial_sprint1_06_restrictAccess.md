[< Tutorial Sprint 1](tutorial_sprint1.md)
# Tutorial - Limit access to the find() operation to users with the proper permission

### Overview
Continue securing our external API, this time implementing [permissions](../develop/develop_permissions.md).



### Start by writing a unit test to verify a user has permission to access the route

By default, your `[plugin]/test/fixtures` directory has some standard files included:

| file name |  description |
|------------|--------------|
| SiteUser.json | Definitions of different Users on the Site |
| PermissionAction.json | Definitions of action permissions on the site |
| PermissionRole.json | Defines a role with ties to defined actions |
| Permission.json | links a user to a Role |

The default SiteUser.json defined a `test` user for the system.  When you use the 
```javascript
request = AD.test.request(function(err){
    done(err);
});
```
command to receive a request object, our `AD.test.request()` method already logs in as the default `test` user for you.

For us to test if access to our route is protected by our permissions, we need to have one test for an authorized user that can access the route, and another test for a user who can't access our route.

We'll leave the default `test` user as the user with the permissions, and create a new user that doesn't have permissions.

So, add a new user the the `[plugin]/test/fixtures/SiteUser.json` file:
```json
[
  {
    "id" : 1,
    "guid": "test",
    "username": "test",
    "password": "test",
    "isActive": 1,
    "languageCode" : "en"
  },
  {
    "id": 2,
    "guid": "testNoPerm",
    "username": "testNoPerm",
    "password": "test",
    "isActive": 1,
    "languageCode" : "en"
  }
]
```

`testNoPerm` is now our user without permissions.

So, lets write some tests to verify things work as we expect. 

Back in [step 3](tutorial_sprint1_03_fixtureResponse.md) of this sprint, we alredy created a unit test to verify we are able to retrieve data back from our route:
```javascript
it('should return data on a request: ', function(done) {

    request
        .get('/opstool-process-approval/parequest')
        .set('Accept', 'application/json')
        .expect('Content-Type', /json/)     // should return json
        .expect(200)                        // should return a successful response.
        .end(function(err, res){

            assert.isNull(err, ' --> there should be no error.');
            assert.isArray(res.body, ' --> should have gotten an array back. ');
            assert.lengthOf(res.body, 3, ' --> should only get 3 of our test entries back.');
            done(err);
        });

});
```
This request object is using our `test` user that is supposed to have permission to access the route.  So if this test works still ... then we know our permissions are working for allowed users.  We don't have to create another test for this case.

We now need to add a test for a user with out permission to make sure they are denied access.  First let's make a request object for the unauthenticated user:
```javascript
// test/controllers/PARequestController.js

var superTest = require('supertest');
var requestNoPerm = null;


describe('PARequestController', function() {

    before(function(done) {
 
        request = AD.test.request(function(err){

            requestNoPerm = superTest.agent(sails.hooks.http.app);

            requestNoPerm
                .post('/site/login')
                .send({username:'testNoPerm', password:'test'})
                .end(function(err, res){
                    done(err);
                });

        });

    });

```

`requestNoPerm` is the request object for our unauthenticated user.  Let's make sure that when it tries to access the route, it gets an error:
```javascript
// test/controllers/PARequestController.js
it('should reject unauthorized users: ', function(done) {

    requestNoPerm
        .get('/opstool-process-approval/parequest')
        .set('Accept', 'application/json')
        .expect(403)                        // should return a forbidden
        .end(function(err, res){

            assert.isNull(err, ' --> there should be no error.');
            done(err);
        });

});
```

Now run your tests:
```sh
$ npm test

> opstool-process-approval@0.0.0 test /sails/node_modules/opstool-process-approval
> make test

  ․․․․․․․․․․

  9 passing (12s)
  1 failing

  1) PARequestController should reject unauthorized users: :
     Uncaught AssertionError:  --> there should be no error.: expected [Error: expected 403 "Forbidden", got 200 "OK"] to equal null
      at Function.assert.isNull (node_modules/chai/lib/chai/interface/assert.js:388:32)
      at Test.<anonymous> (test/controllers/PARequestController.js:101:24)
      at Test.assert (node_modules/supertest/lib/test.js:156:6)
      at Server.assert (node_modules/supertest/lib/test.js:127:12)
      at net.js:1419:10



make: *** [test] Error 1
npm ERR! Test failed.  See above for more details.
```

The error here is that our unauthorized user is being allowed to access the route.  So now let's go edit the code to lock him out.



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


Now lets run our unit tests to see if they are working:
```sh
$ npm test

> opstool-process-approval@0.0.0 test /Users/codingMonkey/Sites/web/sails/node_modules/opstool-process-approval
> make test

  ․․․․․․․․․․

  8 passing (13s)
  2 failing

  1) PARequestController should not be able to access our shortcut route for find: :
     Uncaught AssertionError:  --> there should be no error.: expected [Error: expected "Content-Type" header field] to equal null
      at Function.assert.isNull (node_modules/chai/lib/chai/interface/assert.js:388:32)
      at Test.<anonymous> (test/controllers/PARequestController.js:68:24)
      at Test.assert (node_modules/ad-utils/node_modules/supertest/lib/test.js:156:6)
      at Server.assert (node_modules/ad-utils/node_modules/supertest/lib/test.js:127:12)
      at net.js:1419:10

  2) PARequestController should return data on a request: :
     Uncaught AssertionError:  --> there should be no error.: expected [Error: expected "Content-Type" header field] to equal null
      at Function.assert.isNull (node_modules/chai/lib/chai/interface/assert.js:388:32)
      at Test.<anonymous> (test/controllers/PARequestController.js:84:24)
      at Test.assert (node_modules/ad-utils/node_modules/supertest/lib/test.js:156:6)
      at Server.assert (node_modules/ad-utils/node_modules/supertest/lib/test.js:127:12)
      at net.js:1419:10



make: *** [test] Error 2
npm ERR! Test failed.  See above for more details.
```

The good news:  our unit test to verify unauthorized users are rejected passed.  

The bad news: our previous tests for our authorized user is also being rejected.

No problem:  we just need to make sure our testing fixtures specify that our `test` user is authorized with this action permission.

Add our new `process.approval.tool.view` Action to the `PermissionAction.json` fixture:
```json
[
  {
    "id": 1,
    "action_key": "test.action",
    "createdAt": "2015-05-19 15:37:39",
    "updatedAt": "2015-05-20 15:06:44"
  },
  {
    "id": 2,
    "action_key": "process.approval.tool.view",
    "createdAt": "2015-05-19 15:37:39",
    "updatedAt": "2015-05-20 15:06:44"
  }
]
```

This new action has `id:2`.

Now update our `PermissionRole.json` fixture to include this action in our default test role:
```json
[
  {
    "id": 1,
    "actions":[1,2],
    "createdAt": "2015-05-19 15:37:39",
    "updatedAt": "2015-05-20 15:06:44"
  }
]
```

(the `actions:[]` array should now include our new action id).


Run our tests again:
```sh
$ npm test

> opstool-process-approval@0.0.0 test /sails/node_modules/opstool-process-approval
> make test

  ․․․․․․․․․․

  10 passing (13s)

```


So now it looks like our Permissions are working.  But for us to continue to develop the program, we need to give our current user account (`admin`) permission to `process.approval.tool.view`



### Adding Permission for the admin user to view the page.

In order to allow you to access that resource again, we now need to make sure your user account (`admin`) is assigned to the permission we just created (`process.approval.tool.view`).  To do that, we edit one of the Roles that is assigned to you and then add this action to that role:

+ open up the opsportal in your web browser
+ click [menu] and then the [administration] tab
+ now click on the [roles] menu option and select one of your Roles (`System Admin`)
+ With that role selected, you will now see alist of action permissions
+ scroll down until you see the `process.approval.tool.view` action and click the checkbox.


Now go back to your browser and try to access: `http://localhost:1337/opstool-process-approval/parequest`

You should now see the data that we entered in our `test/fixtures/PARequest.json` file.


---
[< step 5 : Lockdown unused interfaces](tutorial_sprint1_05_lockdownAPI.md) 
[step 7 : .... >](tutorial_sprint1_07_????.md) 