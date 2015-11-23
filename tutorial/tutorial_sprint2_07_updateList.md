[< Tutorial Sprint 2](tutorial_sprint2.md)
# Tutorial - Updating the List

### Overview
The `ApprovalWorkspace` is processing a transaction, but now we need to remove the transaction from our list.


### Listen to can.Model `updated` events
CanJS' implementation of Models will [trigger an event](http://canjs.com/docs/can.Model.html#section_Listentochangesindata) when a model is `created`, `updated` or `destroyed`.  This means we can listen for any of these changes on an **individual** model, or we can request the Base Model to alert us of **any** instance changing state.

In our implementation, we'll take the second option.


After the `PendingTransactions` controller creates it's list, request to be updated when any transaction model is `updated`:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/PendingTransactions.js
            
            // now load our data from the server:
            this.PARequest = AD.Model.get('opstools.ProcessApproval.PARequest');
            this.PARequest.findAll({ status:'pending'})
            .fail(function(err){
console.error('!!! Dang.  something went wrong:', err);
            })
            .then(function(list){
                _this.data.listTransactions = list;
                _this.dom.list.html(can.view('PendingTransactions_List', {items:list}));
console.log('... here is our list of pending transactions:', list);
            });

            // listen for updates to any of our PARequest models:
            this.PARequest.bind('updated', function( ev, request ) {

            });
```

When our event handler gets called, the updated request model will be passed in.  Let's make sure this model's status is no longer `pending` before we act on it.
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/PendingTransactions.js
            
            // listen for updates to any of our PARequest models:
            this.PARequest.bind('updated', function( ev, request ) {

                // only do something if this is no longer 'pending'
                if (request.status != 'pending') {
                    
                }
            });
```

If a transaction is no longer `pending` then we don't want it in our list any more:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/PendingTransactions.js
            
            // listen for updates to any of our PARequest models:
            this.PARequest.bind('updated', function( ev, request ) {

                // only do something if this is no longer 'pending'
                if (request.status != 'pending') {
                    
                    // verify this request is in our displayed list
                    var atIndex = _this.data.listTransactions.indexOf(request);
                    if (atIndex > -1) {

                        // if so, remove the entry.
                        _this.data.listTransactions.splice(atIndex, 1);

                    }
                }
            });
```

Reload the OpsPortal and process a transaction.  The processed transaction should now dissappear.

However, now we have a blank screen left on the page.  Let's go ahead and select the next item in the list after the one that was processed: 
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/PendingTransactions.js
            
            // listen for updates to any of our PARequest models:
            this.PARequest.bind('updated', function( ev, request ) {

                // only do something if this is no longer 'pending'
                if (request.status != 'pending') {
                    
                    // verify this request is in our displayed list
                    var atIndex = _this.data.listTransactions.indexOf(request);
                    if (atIndex > -1) {

                        // if so, remove the entry.
                        _this.data.listTransactions.splice(atIndex, 1);


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
            });
```

I decided to update our click handler for the LI elements to instead call a `.selectLI()` method.  This way I can use it here too. 
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/PendingTransactions.js
            
        selectLI: function($el) {

            this.element.find('.active').removeClass('active');
            $el.addClass('active');

            var model = $el.data('item');
            this.element.trigger(this.options.eventItemSelected, model);
        },



        'li click': function ($el, ev) {

            this.selectLI($el);

            ev.preventDefault();
        },
```

Now reload the OpsPortal and process some of your transactions.

The Good News, the next items in the list are being selected.  
The Bad News, if an item in the list is selected, I expected to see the corresponding form on the `ApprovalWorkspace` but instead we are getting a blank screen.

This was a tricky timing issue to debug.  Basically:

+ the `ApprovalWorkspace`'s `this.transaction.save()` was being executed.
+ once the model performed the `.save()` it emitted the `updated` event
+ the `PendingTransactions` list responded to the `updated` event and then caused the next list item to be selected
+ this was processed all the way through to the `ApprovalWorkspace` creating and displaying the next Transaction template
+ after all that was finished ... then the `ApprovalWorkspace`'s  `this.transaction.save().then()` method was processed.
+ at the end of that method we actually had cleared the form:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ApprovalWorkspace.js

            this.transaction.save()
            .fail(function(err){
                console.error(err);
                _this.buttons[status].ready();
                _this.buttonsEnable();
            })
            .then(function(updatedTrans){
                console.log('... transaction saved!');
                _this.buttons[status].ready();
                _this.buttonsEnable();
                _this.showDOM('none'); // don't show any of our panels
            })

```

So to fix this issue, just remove the `_this.showDOM('none');` command.  (Actually since we have a new template by now, we can remove the button status updates as well):
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ApprovalWorkspace.js

            this.transaction.save()
            .fail(function(err){
                console.error(err);
                _this.buttons[status].ready();
                _this.buttonsEnable();
            })
            .then(function(updatedTrans){
                console.log('... transaction saved!');
            })

```

Reload the OpsPortal and try it out.  Everything works as expected now, except that once we clear out our list, we still have the last form remaining on the screen.   


### Display a final 'All Done' screen
OK, we did not actually think of an 'All Done' screen in our initial proposal, and as a result, we don't have a UI element to display for it.  Not to worry, we'll just copy the `Instructions` div and put our `AllDone` instructions in it.  Then later we'll have our UI designer make this look better.

Add an additional HTML div for our `AllDone` message.
```html
<!-- [plugin]/assets/opstools/ProcessApproval/views/ProcessApproval/ProcessApproval.ejs  -->

            <!-- Main stage area -->
            <div class='col-xs-10 op-container op-stage pa-approvalworkspace'>

                <!-- Instructions Panel -->
                <div class="mockup-display new-activity opsportal-content-area pa-instructionsPanel">
                       <p> Select an entry on the right to begin editing. </p>            
                </div>

                <!-- AllDone Panel -->
                <div class="mockup-display new-activity opsportal-content-area pa-allDonePanel">
                       <p> That's it!  You're all done. </p>            
                </div>
```

Attach to it in our `this.dom` handler:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ApprovalWorkspace.js
        initDOM: function () {

            // this.element.html(can.view(this.options.templateDOM, {} ));
            this.dom = {};
            this.dom.instructions = this.element.find('.pa-instructionsPanel');
            this.dom.allDone = this.element.find('.pa-allDonePanel');

```

Now we need to decide when to show it.  

We know we should show it when the list of Models we have reaches 0 entries.  This can easily be accomplished by listening to changes on the list of transactions that we gathered when creating our `PendingTransactions` list.  

But that list was created in the `PendingTransaction` controller, and now we also want that list in our `ApprovalWorkspace` controller.  So it makes sense to me to have the parent controller be the one place to gather the list, and then pass it down to both of  it's sub controllers.  

Let's have the parent `ProcessApproval` controller gather the list, and pass it to it's sub controllers:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ProcessApproval.js
            // Call parent init
            this._super(element, options);

            this.initDOM();
            this.initControllers();
            this.initEvents();
            this.loadListData();    // <<--- call it here
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
console.log('... here is our list of pending transactions:', list);
            });
        },
```

Now remove the original code to load the list from the `PendingTransactions` controller, and add the `.setList()` method:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/PendingTransactions.js

            this.initDOM();

            this.data = {};
            this.data.listTransactions = null;

            // now get access to the PARequest Model:
            this.PARequest = AD.Model.get('opstools.ProcessApproval.PARequest');

            //
            //  Removed the PARequest .findAll() here
            //


            // listen for updates to any of our PARequest models:
            this.PARequest.bind('updated', function( ev, request ) {

                // only do something if this is no longer 'pending'
                if (request.status != 'pending') {

                    // verify this request is in our displayed list
                    var atIndex = _this.data.listTransactions.indexOf(request);
                    if (atIndex > -1) {

                        // if so, remove the entry.
                        _this.data.listTransactions.splice(atIndex, 1);


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
            });

        },


        // Add this .setList() here:
        setList: function(list){ 
            this.data.listTransactions = list;
            this.dom.list.html(can.view('PendingTransactions_List', {items:list}));
        },
```


And now, let's have the `ApprovalWorkspace` controller receive the list, and then listen for changes in the list's `length`.  If the length is 0 then we show the `allDone` panel:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/PendingTransactions.js

        init: function (element, options) {
            var _this = this;
            options = AD.defaults({
                    // templateDOM: '//opstools/ProcessApproval/views/ApprovalWorkspace/ApprovalWorkspace.ejs'
            }, options);
            this.options = options;

            // Call parent init
            this._super(element, options);


            this.transaction = null;
            this.buttons = {};


            // Keep track of the data like we do in PendingTransactions
            this.data = {};
            this.data.listTransactions = null;


            this.initDOM();

        },

        

        setList:function(list) {
            var _this = this;

            this.data.listTransactions = list;
            
            // listen for changes in the 'length' attribute:
            this.data.listTransactions.bind('length', function() {

                // if the list is empty (length == 0)
                if (_this.data.listTransactions.attr('length') == 0) {

                    // now show the 'AllDone' panel:
                    _this.showDOM('allDone');
                } 
            })
        },

```

Reload the OpsPortal and run through the existing transactions in the list.  When they are all cleared out, then you should see our nifty "All Done" message.  It still doesn't look very good ... but that is up to our UI designer to do.  We're just making it all function properly.


---
[< step 6 : Submitting the Transaction](tutorial_sprint2_06_submit.md)
[step 8 : Catch Template Errors  >](tutorial_sprint2_08_templateErrors.md) 