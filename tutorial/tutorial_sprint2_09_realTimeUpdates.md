[< Tutorial Sprint 2](tutorial_sprint2.md)
# Tutorial - Real Time Updates

### Overview
One of the strengths of the SailsJS framework is how easy it makes responding to [real time updates to models](http://sailsjs.org/documentation/reference/web-sockets/resourceful-pub-sub) on the server.  So in this step, we are going to now listen to changes happening to our data and respond to them on our Client UI.


### gathering pendingTransactions using websockets
The Sails blueprints for our models will automatically take care of alerting us to changes to those models that happen on the server.  These updates will happen over Websockets.

But in order for Sails to automatically know to update you about your models over Websockets ... you have to request the model  **using** Websockets.

Currently our default Model implementation uses standard AJAX requests to access our model's RESTful api.  

Let's tell our model to use Sails' Websockets instead:
```javascript
// [plugin]/assets/opstools/ProcessApproval/models/PARequest.js
steal(
        'appdev',
        'opstools/ProcessApproval/models/base/PARequest.js'
).then( function(){

    // Namespacing conventions:
    // AD.Model.extend('[application].[Model]', {static}, {instance} );  --> Object
    AD.Model.extend('opstools.ProcessApproval.PARequest', {

        useSockets: true,       // <<-----  Just add this.

/*
        findAll: 'GET /opstool-process-approval/parequest',
        findOne: 'GET /opstool-process-approval/parequest/{id}',
        create:  'POST /opstool-process-approval/parequest',
        update:  'PUT /opstool-process-approval/parequest/{id}',
        destroy: 'DELETE /opstool-process-approval/parequest/{id}',
        describe: function() {},   // returns an object describing the Model definition
        fieldId: 'id',             // which field is the ID
        fieldLabel:'actionKey'      // which field is considered the Label
*/
    },{
/*
        // Already Defined:
        model: function() {},   // returns the Model Class for an instance
        getID: function() {},   // returns the unique ID of this row
        getLabel: function() {} // returns the defined label value
*/
    });

});
```

> NOTE: do this in your `/models/PARequest.js` model definition, not your `/models/base/PARequest.js` file.  

Now reload the OpsPortal and make sure the ProcessApproval tool loads again.  

Looks the same right?  

Now, open up an **additional** browser window and pull up the OpsPortal in that window.  Place the windows side by side, and then choose a window to approve an entry in.  What happens?

Bingo!  The other window received the `update` about the Model that was changed and then followed our logic to choose the next item in our list.  

So, without much work on our part, our Clients are responding to data being updated in real time.  Sweet!


### Only update the UI if we made the change
OK, but we don't want to actually have our interface selecting another item in our list if we were not the ones making the change.  
> in Window A: open OpsPortal and select 1st entry in the list
> in Window B: open OpsPortal and select 3rd entry in the List
> in Window B: [Accept] the entry
> notice how the UI switched to the other entry in Window A?  
> that would be very annoying as a user to keep having your UI switch to another entry without you wanting it to

So let's update our `PendingTransactions` controller to keep track of which transaction we have selected:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/PendingTransactions.js
        selectLI: function($el) {

            this.element.find('.active').removeClass('active');
            $el.addClass('active');

            var model = $el.data('item');
            
            this.data.selectedRequest = model;      // <<--- keep track of it here.

            this.element.trigger(this.options.eventItemSelected, model);
        },
```

Change the `updated` handler to not select a new item if the updated model wasn't the one we have selected:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/PendingTransactions.js
            this.data = {};
            this.data.listTransactions = null;
            this.data.selectedRequest = null;       // <<---- initial definition here.

            // now get access to the PARequest Model:
            this.PARequest = AD.Model.get('opstools.ProcessApproval.PARequest');

            // listen for updates to any of our PARequest models:
            this.PARequest.on('updated', function( ev, request ) {

                // only do something if this is no longer 'pending'
                if (request.status != 'pending') {

                    // verify this request is in our displayed list
                    var atIndex = _this.data.listTransactions.indexOf(request);
                    if (atIndex > -1) {

                        // if so, remove the entry.
                        _this.data.listTransactions.splice(atIndex, 1);


                        // if this is the same item we were working on:
                        if (_this.data.selectedRequest  
                            && (_this.data.selectedRequest.getID() == request.getID()) ) { 


                            // we're gonna remove this so clear our selectedRequest:
                            _this.data.selectedRequest = null;


                            // decide which remaining element we want to click:
                            var clickIndx = atIndex;  // choose next one if there.
                            if (_this.data.listTransactions.attr('length') <= clickIndx ) {

                                // not enough entries, so choose the last one then:
                                clickIndx = _this.data.listTransactions.attr('length')-1;

                            }

                            // if there is one to select
                            if (clickIndx >= 0) {

                                // get that LI item:
                                var allLIs = _this.element.find('li');
                                var indexLI = allLIs[clickIndx];

                                // now select this LI:
                                _this.selectLI($(indexLI));

                            } 
                        }

                    }
                }
            });
```


OK, reload the OpsPortal in both windows and try the test again.  This time the remote window does remove the processed item from the list, but it does not switch the user to a different item in the list.  

**However** the remote window is still losing the selected item in the list when the template is redrawn.  So, let's send the `selectedRequest` to the template so we can check for it and add the `active` class.  

Pass in the `data.selectedRequest` value when creating the template:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/PendingTransactions.js
        setList: function(list){ 
            this.data.listTransactions = list;
            this.dom.list.html(can.view('PendingTransactions_List', {items:list, data:this.data}));
        },
```
> NOTE: we are passing in `this.data` to the template instead of `this.data.selectedRequest`.  
> This is because creating the `{ data: this.data.selectedRecord }` would save a **copy of** the current value of `selectedRecord` and send that to the template.  Changing `this.data.selectedRecord` later would not effect the copy sent to the template, so the template would not change.
> But if we save an object like `{ data: this.data }` a **reference to** the object is sent in to the template.  And later on, we can change the `.selectedRecord` property of the object and the template will respond to that change.

Now update the template to assign the `active` class if we have one selected:
```html
<!-- [plugin]/assets/opstools/ProcessApproval/views/ProcessApproval/ProcessApproval.js -->
                          <!-- 
                          items.forEach(function(item) { 

                            var menuData = item.objectData.menu; 
                            if (!menuData) {
                              menuData = item.objectData.objectData.menu;
                            }

                            var active = '';
                            if (data.selectedRequest && data.selectedRequest.getID() == item.getID() ) {
                              active = ' active ';
                            }

                          -->
                            <li mockup-show="new-activity" class="op-container [[= active ]]" obj-embed="item" >
                              <span class="icon"><i class="fa [[= menuData.icon ]] fa-fw"></i></span>
                              <div><strong>[[= menuData.action.key ]]</strong><br>
                                        "[[= menuData.instanceRef ]]" <br>
                                        by "[[= menuData.createdBy ]]"<br>
                                        date: [[= menuData.date ]]
                              </div>
                            </li>
                          <!--
                          });
                          -->
```

Now reload your windows and try it again.  The entry remains selected in the list when the transactions are updated.



### Lock the Item you are editing so others can't work on it
One gotcha still remains, and that is what happens if two users select the same entry in the list and try to work with it at the same time.  

Let's try to implement a `lock` and `unlock` socket messages so our clients will prevent a user from selecting an entry if another client is already editing it.

> NOTE: It might be helpful to read up on some of Sails methods for [working with Sockets](http://sailsjs.org/documentation/reference/web-sockets), and their blueprint [`pubsub` features](http://sailsjs.org/documentation/reference/web-sockets/resourceful-pub-sub).

First, edit our `[plugin]/api/controllers/PARequestController.js` to add two new controller routes: `lock` and `unlock`
```javascript
// [plugin]/api/controllers/PARequestController.js

module.exports = {

    _config: {
        model: "parequest", 
        actions: true,      // <<--- notice I enabled this too
        shortcuts: false,
        rest: true
    },
    

    lock: function(req, res) {
        var id = req.param('id');
        console.log('... lock:', id);
        if (id) {
            PARequest.message(id, {locked:true}, req);
            res.AD.success({locked:id});
        } else {
            res.AD.error(new Error('must provide an id'));
        }
    },


    unlock: function(req, res) {
        var id = req.param('id');
        console.log('... unlock:', id);
        if (id) {
            PARequest.message(id, {locked:false}, req);
            res.AD.success({unlocked:id});
        } else {
            res.AD.error(new Error('must provide an id'));
        }
    }

};
```
>NOTE: remember back when I [locked down the unused interfaces](tutorial_sprint1_05_lockdownAPI.md) I removed the `actions` blueprints because I didn't expect any additional routes above the standard REST interface.  Well, this would be one of those **additional routes** so let's turn on the `actions` option.

Now add `.lock()` and `.unlock()` methods to our client side PARequest model:
```javascript
// [plugin]/assets/opstools/ProcessApproval/models/PARequest.js

    AD.Model.extend('opstools.ProcessApproval.PARequest', {

        useSockets: true,

/*
        findAll: 'GET /opstool-process-approval/parequest',
        findOne: 'GET /opstool-process-approval/parequest/{id}',
        create:  'POST /opstool-process-approval/parequest',
        update:  'PUT /opstool-process-approval/parequest/{id}',
        destroy: 'DELETE /opstool-process-approval/parequest/{id}',
        describe: function() {},   // returns an object describing the Model definition
        fieldId: 'id',             // which field is the ID
        fieldLabel:'actionKey'      // which field is considered the Label
*/
    },{

        lock:function() {
            return AD.comm.socket.get({ url: '/opstool-process-approval/parequest/lock/'+this.getID() });
        }, 

        unlock:function() {
            return AD.comm.socket.get({ url: '/opstool-process-approval/parequest/unlock/'+this.getID() });
        }
/*
        // Already Defined:
        model: function() {},   // returns the Model Class for an instance
        getID: function() {},   // returns the unique ID of this row
        getLabel: function() {} // returns the defined label value
*/
    });
```
> NOTE: implement these methods down in the model instance section.

Make sure that when a model is selected we now lock it:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/PendingTransactions.js
        selectLI: function($el) {

            // if we had locked a previous entry, then unlock it:
            if (this.data.selectedRequest) {
                this.data.selectedRequest.unlock();
            }


            this.element.find('.active').removeClass('active');
            $el.addClass('active');

            var model = $el.data('item');
            this.data.selectedRequest = model;

            // lock the newly selected model:
            this.data.selectedRequest.lock();

            this.element.trigger(this.options.eventItemSelected, model);
        },
```
> NOTE: we also `unlock` any existing model that is no longer selected.

In our `PendingTransactions` list we want to now listen for these new lock status messages and update our list items to reflect their locked status:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/PendingTransactions.js
        init: function (element, options) {
            var _this = this;
            options = AD.defaults({
                    eventItemSelected : 'hey.got.one'
                    // templateDOM: '//opstools/ProcessApproval/views/PendingTransactions/PendingTransactions.ejs'
            }, options);
            this.options = options;

            // Call parent init
            this._super(element, options);


            /*
             *  Removed all this stuff to make it simpler to follow:
             *
             *  just put this at the end of the init() method:
             */


            this.PARequest.on('messaged', function( ev, data ) {


                // one of our transactions was messaged

                // see if we have an LI for this transaction:
                var foundEL = _this.element.find('[parequest-id="'+data.id+'"]');
                if (data.data.locked) {
                    foundEL.addClass('parequest-locked');
                } else {
                    foundEL.removeClass('parequest-locked');
                }

            })

        },
```

We are trying to look up LI entries that have `[parequest-id]` attributes, so let's add that to our template:
```html
<!-- [plugin]/assets/opstools/ProcessApproval/views/ProcessApproval/ProcessApproval.js -->
                            <li mockup-show="new-activity" class="op-container [[= active ]]" parequest-id="[[= item.getID() ]]" obj-embed="item" >
                              <span class="icon"><i class="fa [[= menuData.icon ]] fa-fw"></i></span>
                              <div><strong>[[= menuData.action.key ]]</strong><br>
                                        "[[= menuData.instanceRef ]]" <br>
                                        by "[[= menuData.createdBy ]]"<br>
                                        date: [[= menuData.date ]]
                              </div>
                            </li>
```


And we are adding a newly defined class to the LI entry: `parequest-locked`.  We need to define that css class:
```css
// [plugin]/assets/opstools/ProcessApproval/ProcessApproval.css
li.parequest-locked { 
    background-color: #333333; 
}
```

Now reload your OpsPortals in both browsers.  In one browser click on entries in your list, and see what happens to the entries in the other browser.

Cool huh?  

Also, ugly.  But making this look good is going to be a follow up task for my UI designer.  Now he needs to edit the `li.parequest-locked` definition to make this entry look better. (choose a better background color, add a lock icon to the upper corner, etc..)

But wait!  If you click on the locked entry in the second browser ... it still selects it!  That's not what we want.  Update the `click` handler to not process the click if the LI has a `parequest-locked` class:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/PendingTransactions.js
        'li click': function ($el, ev) {

            if (!$el.hasClass('parequest-locked')) {
                this.selectLI($el);
            }

            ev.preventDefault();
        }
```

Now try it again.  That's more like it.



### listen for 'stale' notifications and refresh our list.
We are locking and processing updated events just fine, but we are not yet being alerted when a **new** entry is being created on the server.

Normally, Sails' blueprints will keep track of any socket connection that requested a `.find()` or `.findAll()` type of API request and automatically sign them up for automatic updates when a new entry is `created`.  Since we have done that in our project, our clients have been assigned to receive automatic updates already.

But, we will only get updated if the `created` event happens as a result of an API call.  In our project we have [disabled those API calls](tutorial_sprint1_05_lockdownAPI.md).  Instead, we are manually creating entries in response to our [message queue callback](tutorial_sprint1_08_messageQueue.md).  If we wanted that server side action to alert us of `created` events, we would need to have the message queue callback perform a .[publishCreate()](http://sailsjs.org/documentation/reference/web-sockets/resourceful-pub-sub/publish-create) alert and we would receive those notifications.

**BUT** ... that isn't what we want in our application.

Due to our implementation of permissions and scopes, just because a new entry is created, doesn't mean everyone using the App should be told about it.  We have already worked through the process of [how to restrict a users access to infomation according to their scope](tutorial_sprint1_07_restrictScope.md) on our server side policies.  I would rather not try to re-implement that logic here.

Instead, we are going to inform all our connected users that their view of their information is potentially `stale`.  Once they get that message, they can then perform another `.find()` to see if they have access to any more entries then they already have shown.  If more entries are returned, then we will add them to our list of `PendingTransactions`.

So, let's start by updating our message queue callback to update our connected users their view is `stale`:
```javascript
//[plugin]/config/bootstrap.js
        if (!allPropertiesFound) {

            // data provided not in valid format

        } else {

            // reformat the incoming data into a PARequest format:
            var paData = {};
            paData.objectData = data;
            paData.actionKey = data.permission.actionKey;
            paData.userID = data.permission.userID;
            paData.callback = data.callback.message;
            paData.status = 'pending';

            PARequest.create(paData)
            .then(function(newEntry){       // <<--- just add this .then() handler

                // tell all connected sockets that their info is "stale"
                sails.sockets.broadcast('sails_model_create_parequest', 'parequest', { verb:'stale'});
            })
            .catch(function(err){
                ADCore.error.log('unable to create this PARequest entry', {
                    error:err,
                    data:paData,
                    module:'opstool-process-approval'
                })
            });
        }
```

In this case, I'm using the [sails.sockets.broadcast()](http://sailsjs.org/documentation/reference/web-sockets/sails-sockets/sails-sockets-broadcast) routine to send the message:

| parameter |  value |  |
|------------|--------------|----------|
| roomName| 'sails_model_create_parequest' | the default 'room' for create events on our 'parequest' model (format: sails_model_create_[modelName]) |
| eventName | 'parequest' | passing the name of the model as the `eventName` allows our client to pass the event to our `PARequest` Model Object |
| data | `data.verb` = 'stale' | if we are going to pass the event to our client side Model, our data should keep a similar format as the standard [.pubsub](http://sailsjs.org/documentation/reference/web-sockets/resourceful-pub-sub) data formats.  Specifically we need to set the `.verb` parameter. |

Now on our client, we can listen for the `stale` event:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ProcessApproval.js
        loadListData:function() {
            var _this = this;

            // now load our data from the server:
            this.PARequest = AD.Model.get('opstools.ProcessApproval.PARequest');
            this.PARequest.findAll({ status:'pending'})
            .fail(function(err){
console.error('!!! Dang.  something went wrong:', err);
            })
            .then(function(list){
                _this.controllers.PendingTransactions.setList(list);
                _this.controllers.ApprovalWorkspace.setList(list);
console.log('... here is our list of pending transactions:', list);
            });

            this.PARequest.on('stale', function(m,d){       // <<---- listen for 'stale' notifications

                // look up new transactions

            })
        },
```

On this event, we are going to request any `pending` transactions we don't already have in our list:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ProcessApproval.js
            this.PARequest.on('stale', function(m,d){

                // we only want pending transactions:
                var condition = {
                    status: "pending"
                };


                // ignore any existing transactions in our list:
                var currentIDs = [];
                _this.data.list.forEach(function(item){
                    currentIDs.push(item.getID());
                })
                if (currentIDs.length > 0) {
                    condition.id = { 
                        '!':currentIDs 
                    };
                };

                _this.PARequest.findAll(condition)
                .fail(function(err){
console.error('!!! Dang.  something went wrong:', err);
                })
                .then(function(listNew){
                    listNew.forEach(function(item){
                        _this.data.list.push(item);
                    })
                })

            })
```

Notice that I added a `_this.data.list` object to track the list of entries our app receives.  Let's make sure we are properly setting that value up:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ProcessApproval.js
        init: function (element, options) {
            var self = this;
            options = AD.defaults({
                    templateDOM: '//opstools/ProcessApproval/views/ProcessApproval/ProcessApproval.ejs',
                    resize_notification: 'ProcessApproval.resize',
                    tool:null   // the parent opsPortal Tool() object
            }, options);
            this.options = options;

            // Call parent init
            this._super(element, options);

            this.data = {};                 // <<---- define it here
            this.data.list = null;

            this.initDOM();
            this.initControllers();
            this.initEvents();
            this.loadListData();

        },


        loadListData:function() {
            var _this = this;

            // now load our data from the server:
            this.PARequest = AD.Model.get('opstools.ProcessApproval.PARequest');
            this.PARequest.findAll({ status:'pending'})
            .fail(function(err){
console.error('!!! Dang.  something went wrong:', err);
            })
            .then(function(list){
                _this.controllers.PendingTransactions.setList(list);
                _this.controllers.ApprovalWorkspace.setList(list);

                _this.data.list = list;     // <<---- Save it here

console.log('... here is our list of pending transactions:', list);
            });
```

OK, now restart sails, and reload your Apps in the browser.  Now practice procesing transactions and waiting to see if new transactions appear in our client UI.



//// TODO: still selects a locked element if it is the one before an entry we just processed...

---
[< step 8 : Catch Template Errors](tutorial_sprint2_08_templateErrors.md)
[step 10 : Form validations  >](tutorial_sprint2_10_formValidations.md) 