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

I decided to update our click handler for the LI elements to instead call a `.selectLI()` method.  This way I can use it here to. 
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

So to fix this issue, just remove the `_this.showDOM('none');` command.  (Actually since we have a new template by now, we can remove the button status code as well):
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
---
[< step 6 : Submitting the Transaction](tutorial_sprint2_06_submit.md)
[step 8 : updating the list  >]() 