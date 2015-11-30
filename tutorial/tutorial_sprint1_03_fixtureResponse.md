[< Tutorial Sprint 1](tutorial_sprint1.md)
# Tutorial - Return our expected Fixture data on PARequest.find()

Now that our fixture data is properly setup, let's make sure that it is getting returned when a request for our PARequest entries are returned.

##### let's write a test to make sure our data is returned 
This is going to be a test for the `api/controllers/PARequestController.js` file.

So create a new unit test file : 
```javascript
// test/controllers/PARequestController.js
var assert = require('chai').assert;

describe('PARequestController', function() {


});
```

Now in our unit tests for Controllers, we use the [supertest](https://github.com/visionmedia/supertest) package to simplify our interactions with our controllers.  But in order to access our controllers, our requesting object needs to login.  

To do that:
```javascript
// test/controllers/PARequestController.js
var assert = require('chai').assert;

var superTest = require('supertest');
var request = null; 


describe('PARequestController', function() {

    before(function(done) {

        request = superTest.agent(sails.hooks.http.app);

        request
            .post('/site/login')
            .send({username:'test', password:'test'})
            .end(function(err, res){
                done(err);
            })
    });

});
```

And we need to make sure we have a fixture defined for a test user in place:
```javascript
// test/fixtures/SiteUser.json
[
  {
    "guid": "test",
    "username": "test",
    "password": "test",
    "isActive": 1,
    "languageCode" : "en"
  }
]
```

> NOTE: our `appdev opstool` command will include some additional fixtures for you automatically:  SiteUser.json is one of them.  So this could already be installed for you.

Now let's add our actual test to our unit test:
```javascript
// test/controllers/PARequestController.js
var assert = require('chai').assert;

var superTest = require('supertest');
var request = null; 


describe('PARequestController', function() {

    before(function(done) {

        request = superTest.agent(sails.hooks.http.app);

        request
            .post('/site/login')
            .send({username:'test', password:'test'})
            .end(function(err, res){
                done(err);
            })
    });


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

});
```

OK, now running your tests should produce an error due to our new unit test:
```sh
$ npm test

> opstool-process-approval@0.0.0 test /Users/.../sailsv11/node_modules/opstool-process-approval
> make test



  ․․․․․․

  5 passing (17s)
  1 failing

  1) PARequestController should return data on a request: :
     Uncaught AssertionError:  --> should only get 3 of our test entries back.: expected [ Array(4) ] to have a length of 3 but got 4
      at net.js:1419:10



make: *** [test] Error 1
npm ERR! Test failed.  See above for more details.

```


> NOTE: because of [Sail's blueprint api](http://sailsjs.org/documentation/reference/blueprint-api), we automatically get back all the test data fixture that we [created in sprint 0](tutorial_sprint0_05_designFixtures.md).  But as part of our design, one of those test entries should not be returned, so this test fails on that case.


##### Now that we have a successfully failing test ... let's update our controller to implement it:
First, we are going to overwrite the standard blueprint api for the PARequestController to return all but one of our test cases.

Our current controller looks like:
```javascript
// api/controllers/PARequestController.js
module.exports = {

    _config: {
        model: "parequest", // all lowercase model name
        actions: true,
        shortcuts: true,
        rest: true
    }

};
```

The controller specifies which model it is asssociated with, and which blueprint apis to implement: `actions`, `shortcuts`, and `rest`.

The Sails blueprints maps the incoming requests to several standard controller methods that get added for you: `find`, `create`, `update`, `destroy`, `populate`, `add` and `remove`.

In this case, we want to override the `find` method:
```javascript
// api/controllers/PARequestController.js
var fs = require('fs');
var path = require('path');
var fixtureData = null;


module.exports = {

    _config: {
        model: "parequest", // all lowercase model name
        actions: true,
        shortcuts: true,
        rest: true
    },


    find: function(req, res) {

        if (fixtureData == null){

            var pathToFixture = path.join(__dirname,'..', '..', 'test', 'fixtures', 'PARequest.json');
            fs.readFile(pathToFixture, { encoding:'utf8'}, function(err, data) {

                if (err) {
                    res.error(err);
                } else {

                    fixtureData = JSON.parse(data);
                    fixtureData.splice(fixtureData.length-1, 1);  // <-- this one isn't supposed to be returned.
                    res.send(fixtureData);
                }
            });

        } else {

          res.send(fixtureData);
        }

    }

};
```
> NOTE: you may be wondering why I'm going through all the trouble to remove 1 entry from the data.  Bascially that entry is supposed to be an entry the test user doesn't have permission to see.  I want this `find()` method to return valid entries for our testing purposes.  In future steps we will actually implement permissions and this will make more sense.


##### Verify our tests pass

Now let's look at our tests:
```sh
$ npm test

> opstool-process-approval@0.0.0 test /Users/.../sailsv11/node_modules/opstool-process-approval
> make test



  ․․․․․․

  6 passing (1m)

```

Good so far.


##### check the live RESTful interface

Now let's start up sails and check to see the running RESTful GET operation:
```sh
# in your sails root
$ sails lift
```

Once Sails starts, open your browser and enter the url:  `http://localhost:1337/opstool-process-approval/parequest`

>NOTE: if you installed Sails with local authentication, you will initially be taken to a local login screen.  After entering the default Admin credentials (username:`admin`, password:`admin`) you will be shown the results of the request.


### Summary

OK, at this point, we now have a live RESTful GET operation that returns the set of data we have approved.  

Now I could divide this project between two developers (a client side developer, and a server side developer) and have them work independently using this set of data as a contract between them.

The server side developer would create his own feature branch of the project, and then put the pieces together on the server to make the find operation produce the data in the format we have specified.

The client side devleoper would create his own feature branch of the project, and then design the client side code to display the data returned in the format we have specified.

If in the upcoming sprints we make changes in the data format, then we only have to change that data in one place (`/test/fixtures/PARequest.json`) and both the client and server side developers can update their branches and continue their work.



##### Final Note:

In our unit test, we have provided a simpler function to provide a logged in request object: `AD.test.request(cb)`

you can change the PARequestController test to look like this:
```javascript
// test/controllers/PARequestController.js
var assert = require('chai').assert;

// NOTE: I removed supertest and replaced it with 'ad-utils'
var AD = require('ad-utils');
var request = null; 

describe('PARequestController', function() {

    before(function(done) {
 
        request = AD.test.request(function(err){
            done(err);
        });

    });


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

});
```

---
[< step 2 : Correct our Fixture data](tutorial_sprint1_02_fixtures.md) 
[step 4 : Create our Server Side Feature branch >](tutorial_sprint1_04_serverBranch.md) 