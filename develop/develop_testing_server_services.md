[< Develop](Develop.md)
# Sails/Server side tests: Services
Services are Sail's way of exposing logic globally to the Sails environment.  One or more Sails resources can access a Service.

Read up on how Sails implements [services](http://www.sailsjs.org/documentation/concepts/services).

When trying to write unit tests for a service keep some things in mind:

+ services usually access Models to process their results
+ Models will need to be "mocked" by a Unit Test so you can focus on the fn() being tested
+ 

#### Mocking Models in Services

When designing a service that can easily be tested, make sure you can replace the models your service is using with your mocks.

So, if we are creating a `[plugin]/api/services/PluginService.js` service,

###### 1. Create local variables for the Models you are accessing:
   ```javascript
   var Model1 = null;  // Will point to PluginModel1
   var Model2 = null;  // Will point to PluginModel2
   ```

###### 2. Create an `__init()` method that will assign those variables to your actual Models:
   ```javascript
   __init: function(cb) {
       Model1 = PluginModel1;
       Model2 = PluginModel2;
       cb();
   }
   ```
   > Note: make sure you conform to the standard nodejs way of handling callbacks.

###### 3. When your plugin is booting up, make sure that init is called:
   ```javascript
   // in your [plugin]/config/bootstrap.js  file:
   module.exports = function (cb) {
       PluginService.__init(cb);
   };
   ```

   or if you have multiple services to setup:
   ```javascript
   // in your [plugin]/config/bootstrap.js  file:
   module.exports = function (cb) {
       async.parallel([
          PluginService1.__init,
          PluginService2.__init,
          ... 
          PluginServiceN.__init
       ], function(err, results){

          cb(err);
       })
   };
   ```

###### 4. Create a __MockMe() routine to allow your unit tests to replace those Models:
   ```javascript
   __mockMe: function(opt) {

       Model1 = opt.Model1 || Model1; 
       Model2 = opt.Model2 || Model2; 
   }
   ```

   and a way to figure out your current values:
   ```javascript
   __realMe: function() {

      return {
      	Model1 : Model1,
      	Model2 : Model2
      }
   }
   ```


###### 5. Setup Fixtures for the models you want to Mock:
   
   > TODO: tell Ric to let Johnny fill this out one day.


#### Write tests for your services

The unit tests for our server side code reside in: `[plugin]/tests/unit/`

Our naming convention for our service tests: service_[PluginService]_[methodTested].js

So, if we are creating a `[plugin]/api/services/PluginService.js` service with a method named `exampleFn` then our test would be: `[plugin]/tests/unit/service_PluginService_exampleFn.js`

```javascript
var assert = require('chai').assert;
describe('test PluginService.exampleFn()',function(){

    it('calling .exampleFn() should return a Deferred:',function(done){

        var originalObjects = PluginService.__realMe();

        // we know what these fixtures will return
        PluginService.__mockMe({
        	Model1: sails.__testing.fixtures.Plugin.Model1,
        	Model2: sails.__testing.fixtures.Plugin.Model2
        })


        var result = PluginService.exampleFn();

        assert.property(result, 'then', '---> should have a then() method');
        assert.property(result, 'fail', '---> should have a fail() method');

        result.fail(function(err){
            assert.ok(false, ' --> should not have called this. ');

            // return me back to our original state:
            PluginService.__mockMe(originalObjects);
            done();
        })
        .then(function(data){
            assert.ok(true, ' --> should have been called. ');

            // return me back to our original state:
            PluginService.__mockMe(originalObjects);
            done();
        });

    });

});
```



[< Sails/Server side tests](develop_testing_server.md)