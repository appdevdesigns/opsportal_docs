[< Tutorial Sprint 1](tutorial_sprint1.md)
# Tutorial - Correct our Fixture data

Back in [Sprint 0](tutorial_sprint0_05_designFixtures.md), I created some fixture data in order to create a set of reference data for our application.

This data should be considered the standard by which we judge the implementation.  

##### Let's make sure these fixtures are working in our unit tests:
[When I first created the fixtures](tutorial_sprint0_05_designFixtures.md), I named the files 

+ `test/fixtures/approvalrequests.json` 
+ `test/fixtures/approvalcomments.json`.  

But I just named our models `PARequest` and `PAComment`, so we will need to rename those fixtures to:

+ `test/fixtures/PARequest.json`
+ `test/fixtures/PAComment.json`


Now let's update our test for PARequest to verify our fixture data is being loaded:
```javascript
// test/models/PARequest.js
var assert = require('chai').assert;

describe('PARequest', function() {


    it('should be there', function() {

        assert.isDefined(PARequest, ' --> PARequest should be defined!');

    });


    it('should load the PARequest fixtures', function(done){

        PARequest.find()
        .exec(function(err, list) {
            assert.isAbove(list.length, 0, ' --> should be more than 0 entries.');
            done();
        });

    })

});
```

and 

```javascript
var assert = require('chai').assert;

describe('PAComment', function() {


    it('should be there', function() {

        assert.isDefined(PAComment, ' --> PAComment should be defined!');

    });


    it('should load the PAComment fixtures', function(done){

        PAComment.find()
        .exec(function(err, list){
            assert.isAbove(list.length, 0, ' --> should be more than 0 entries.');
            done();
        });

    });

});
```

now run the tests and see if they pass:
```sh
$ npm test

> opstool-process-approval@0.0.0 test /Users/.../sailsv11/node_modules/opstool-process-approval
> make test



  ․․․․․

  5 passing (34s)

```

Sweet!


### But hold on a minute!  
If you look in your DB now, the tables for `pa_request` and `pa_comment` have been populated with your fixture data.  

This is **NOT** what we wanted to happen.

In our `test/bootstrap.test.js` file, we load sails, and tell sails to use our `test` DB connection by default:
```javascript
 AD.test.sails.load({
    models:{
      connection:'test',
      migrate:'drop'
    }
  })
```

This is supposed to be using a local `sails-disk` adaptor and not overwriting your DB data.

The problem is in your model definitions.  The models specified a connection manually, so that will always override the settings in Sails when it loads.  To fix this, comment out the connection settings:

```javascript
// api/models/PARequest.js
module.exports = {

  // connection:"appdev_default",   // <--- comment out the connection

  tableName:"pa_request",   

  attributes: {

    actionKey : { type: 'string' },

    userID : { type: 'string' },

    callback : { type: 'string' },

    status : { type: 'string' },

    objectData : { type: 'json' },

    comments: {
      collection:'pacomment',  
      via:'request'
    }
  }
};
``` 

and 

```javascript
// api/models/PAComment.js
module.exports = {

  // connection:"appdev_default",    // <--- comment out the connection

  tableName:"pa_comment",  

  attributes: {

    comment : { type: 'text' },

    response : { type: 'text' },

    request: {
      model:'parequest'  
    }
  }
};
```

Now clear your DB tables, run your tests again, and check to verify that the data is not being stored in your DB tables.


---
[< step 1 : Create the Models on the server](tutorial_sprint1_01_models.md)
[step 3 : Return our expected Fixture data on PARequest.find() >](tutorial_sprint1_03_fixtureResponse.md) 