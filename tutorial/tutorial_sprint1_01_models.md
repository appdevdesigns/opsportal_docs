[< Tutorial Sprint 1](tutorial_sprint1.md)
# Tutorial - Creating the Models on the Server


### Overview
Creating our SailsJS Models for the project.


### Prerequsite
I'm expecting you to be familiar with our [developer Model notes](../develop/develop_models.md).


### Let's Go
According to our design document, I am expecting there to be two tables: ApprovalRequests and ApprovalComments.

Some decisions:

+ I need to namespace these Models so they don't conflict with other Models in our OpsPortal ... but I also want to keep the Model name small enough to not have to write a Book each time I want to use one.  So, using 'PA' for Process Approval, I'm going to call the models: `PARequest` and `PAComment`.
>NOTE: notice the model names are singular

+ These Models are new to our system so I'm just going to let Sails manage them and create them in our MySQL database.
+ it is just fine to let these models sit in the default DB connection for our sails installation.


##### Be a good TDD developer and write your tests first
At this point, I don't have any idea about any unique capabilities I'm expecting from either of my Models, so my tests wont be very specific.  I'll create a test case for each of my Models and simply check for their existance:


```javascript
// test/models/PARequest.js
var assert = require('chai').assert;

describe('PARequest', function() {


    it('should be there', function() {

        assert.isDefined(PARequest, ' --> PARequest should be defined!');

    });

});
```

and 

```javascript
// test/models/PAComment.js
var assert = require('chai').assert;

describe('PAComment', function() {


    it('should be there', function() {

        assert.isDefined(PAComment, ' --> PAComment should be defined!');

    });

});
```

Now running tests on my project shows 2 failures:

```sh
$ npm test

> opstool-process-approval@0.0.0 test /Users/.../sailsv11/node_modules/opstool-process-approval
> make test



  ․․․

  1 passing (16s)
  2 failing

  1) PAComment should be there:
     ReferenceError: PAComment is not defined
  

  2) PARequest should be there:
     ReferenceError: PARequest is not defined
  



make: *** [test] Error 2
npm ERR! Test failed.  See above for more details.
```


##### Now let's create our models
```sh
# from our [pluginRoot]
$ appdev resource opstools/ProcessApproval PARequest actionKey:string userID:string callback:string status:string objectData:json
$ appdev resource opstools/ProcessApproval PAComment comment:text response:text

```

>NOTE: the first time you run the `appdev resource` command, it will ask you for a default model connection to use.  In this case we are simply using the `appdev_default` entry, so just hit [return].  The command will remember your settings and not ask you again.  You can always override the default entry by adding a `connection:[connectionName]` to the command line

By default, Sails will create mysql tables named: `parequest` and `pacomment`.  However, I want to name space these tables as `pa_request` and `pa_comment`.  *I know, big difference.*  

So I edit the Sails model definitions like so:
```javascript
// api/models/PARequest.js
module.exports = {

  connection:"appdev_default",

  tableName:"pa_request",   // <-- namespaced with 'pa_'

  attributes: {

    actionKey : { type: 'string' },

    userID : { type: 'string' },

    callback : { type: 'string' },

    status : { type: 'string' },

    objectData : { type: 'json' }
  }
};
```

and 

```javascript
// api/models/PAComment.js
module.exports = {

  connection:"appdev_default",

  tableName:"pa_comment",  // <-- namespaced with 'pa_'

  attributes: {

    comment : { type: 'text' },

    response : { type: 'text' }
  }
};
```

Also ... *(I should probably write a test for this first, but I'm in the moment)* ... I know from the design document that these models are related, so I'm just gonna go ahead and define their [associations](http://www.sailsjs.org/documentation/concepts/models-and-orm/associations):

PARequest can have many PAComment :
```javascript
// api/models/PARequest.js
module.exports = {

  connection:"appdev_default",

  tableName:"pa_request",   

  attributes: {

    actionKey : { type: 'string' },

    userID : { type: 'string' },

    callback : { type: 'string' },

    status : { type: 'string' },

    objectData : { type: 'json' },

    comments: {
    	collection:'pacomment',  // <-- all lowercase!
    	via:'request'
    }
  }
};
```

And PAComment belongs to only one PARequest:
```javascript
// api/models/PAComment.js
module.exports = {

  connection:"appdev_default",

  tableName:"pa_comment",  

  attributes: {

    comment : { type: 'text' },

    response : { type: 'text' },

    request: {
    	model:'parequest'  // <-- all lowercase
    }
  }
};
```


##### OK, before we go any further, lets verify our tests work now
```sh
$ npm test

> opstool-process-approval@0.0.0 test /Users/.../sailsv11/node_modules/opstool-process-approval
> make test



  ․․․

  1 passing (16s)
  2 failing

  1) PAComment should be there:
     ReferenceError: PAComment is not defined
  

  2) PARequest should be there:
     ReferenceError: PARequest is not defined
  



make: *** [test] Error 2
npm ERR! Test failed.  See above for more details.
```

Wait!  What? 


##### Oh ... yeah ...
We have added these files in our plugin directory, but the parent sails install doesn't yet know about them.  When our tests run, it starts our parent sails instance ... which still doesn't know about our models.

So, run the `setup.js` file to make sure the files in our plugin is properly setup:
```sh
# from your [pluginRoot]
$ node setup/setup.js
```

now run our tests:
```sh
$ npm test

> opstool-process-approval@0.0.0 test /Users/.../sailsv11/node_modules/opstool-process-approval
> make test



  ․․․

  3 passing (19s)

```

Sweet.




---
[< Tutorial Sprint 1](tutorial_sprint1.md)
[step 2 : Correct our Fixture data >](tutorial_sprint1_02_fixtures.md) 