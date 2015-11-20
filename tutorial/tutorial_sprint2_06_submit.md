[< Tutorial Sprint 2](tutorial_sprint2.md)
# Tutorial - Submitting the Transaction


### Overview
Now that we have a transaction in place, let's handle the Submit or Reject actions.

> NOTE: 
> One of the entries in our fixture data is providing us with an invalid template.
> We are not handling that display error until [step 8], so as you walk through the next two steps of the tutorial, clicking on that entry in our list will result in a blank screen with an error on your console.
> It is possible if you click on enough other entries in the list and process them, that your list will fill up with these invalid entries.  
> in that case, you need to clear your DB table and let our server side test routine fill it back up for you.


### Responding to the Button Clicks
The `ApprovalForm` has two buttons: [Accept] and [Deny].

Let's listen to the button clicks and update the current Transaction accordingly.  

Since I'm not sure what HTML will be displayed in the Object Template, I don't want to assume I can simply find any button within my `ApprovalWorkspace` and listen to those clicks.  So instead, I'll mark the `ApprovalForm` buttons with a unique css class: `pa-approvalform-submit`
```html
<!-- [plugin]/assets/opstools/ProcessApproval/views/ProcessApproval/ProcessApproval.ejs  -->

<div class="op-container op-block nomargintop">
    <button class="btn btn-success float-right pa-approvalform-submit" >Accept</button>
    <button class="btn btn-info  float-right offset-15 pa-approvalform-submit mockup" >Request</button>
    <button class="btn btn-warning float-right  offset-15 pa-approvalform-submit"><i class="fa fa-hand-paper-o"></i>Deny</button>
</div>
```

now tell our `ApprovalWorkspace` controller to respond to `click` events on those elements:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ApprovalWorkspace.js
        '.pa-approvalform-submit click': function($el, ev) {
console.log('button clicked!');
            ev.preventDefault();
        }
```

I want to distinguish the differences between the buttons, so I'll add an attribute `pa-status` with either `approved`, `rejected` or `requesting` as it's value. 
> NOTE: these correspond the the allowed values we specified back in [Sprint 1](tutorial_sprint1_08_messageQueue.md) (just scroll down you'll find it).
```html
<!-- [plugin]/assets/opstools/ProcessApproval/views/ProcessApproval/ProcessApproval.ejs  -->

<div class="op-container op-block nomargintop">
    <button class="btn btn-success float-right pa-approvalform-submit" pa-status="approved" >Accept</button>
    <button class="btn btn-info  float-right offset-15 pa-approvalform-submit mockup" pa-status="requesting" >Request</button>
    <button class="btn btn-warning float-right  offset-15 pa-approvalform-submit" pa-status="rejected"><i class="fa fa-hand-paper-o"></i>Deny</button>
</div>
```


Now update our click handler to grab the `pa-status` value and update our current transaction with it:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ApprovalWorkspace.js
        '.pa-approvalform-submit click': function($el, ev) {
console.log('button clicked!');

            this.transaction.attr('status', $el.attr('pa-status'));
            this.transaction.save()
            .fail(function(err){
                console.log(err);
            })
            .then(function(updatedTrans){
                console.log('... transaction saved!');
            })
            ev.preventDefault();
        }
```

Now reload the OpsPortal and select an entry in your `PendingTransactions` list.  With that entry displayed in the `ApprovalWorkspace`, click on the [Accept] button.

You'll see the `... transaction saved!` message in the console.
You'll see a network request for `put opstool-proces-approval/parequest/1`
And if you look in your DB.pa_request table, you will see the status has changed to 'approved'.

However, the form still remains on the screen.
And worse yet, if you sit and rapidly click on the button, you will fire off a network request for each click!  

### Disable the buttons while waiting for the transaction to save
Our OpsPortal provides an `OpsBusyButton` object we can use to manage our buttons states.  To use it we first include the object as a dependency to our `ApprovalWorkspace` controller:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ApprovalWorkspace.js
steal(
        // List your Controller's dependencies here:
        'appdev',
        'OpsPortal/classes/OpsButtonBusy.js',
function(){
```

Then in our `init` constructor we create a holder for our buttons:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ApprovalWorkspace.js
        init: function (element, options) {
            var _this = this;
            options = AD.defaults({
                    // templateDOM: '//opstools/ProcessApproval/views/ApprovalWorkspace/ApprovalWorkspace.ejs'
            }, options);
            this.options = options;

            // Call parent init
            this._super(element, options);


            this.transaction = null;
            this.buttons = {};          // <<----  Here

            this.initDOM();

        },
```

Now we attach the `OpsButtonBusy` Object to our buttons.  Keep in mind that the buttons are actually part of our `ApprovalForm` which is a template that gets destroyed and recreated each time a new transaction is selected in our `PendingTransactions` list.  So we need to attach our `OpsButtonBusy` to the buttons after the template is created:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ApprovalWorkspace.js
        setTransaction: function ( transaction ) {
            var _this = this;

            console.warn('*** ApprovalTransaction: received a transaction:', transaction);
            this.transaction = transaction;

            this.dom.approvalForm.html( can.view('PA_ApprovalForm', {transaction: this.transaction}));

            this.embeddTemplate('.pa-approvalForm-objTemplate', this.transaction.objectData.form);
            this.embeddTemplate('.pa-approvalForm-relatedTemplate', this.transaction.objectData.relatedInfo);

            // for each of my buttons (referenced by pa-status values)
            ['approved', 'rejected'].forEach(function(status){
                _this.buttons[status] = new AD.op.ButtonBusy(_this.element.find('[pa-status="'+status+'"]'));
            })
            
            this.showDOM('approvalForm');
        },
```
> NOTE: I chose to name the buttons the same as our `pa-status` values, in order to make referencing the clicked button easier.

Now set the button that was clicked to a `busy` state:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ApprovalWorkspace.js
        '.pa-approvalform-submit click': function($el, ev) {
console.log('button clicked!');
            var _this = this;
            
            var status = $el.attr('pa-status');
            
            this.buttons[status].busy();
            
            this.transaction.attr('status', status);
            this.transaction.save()
            .fail(function(err){
                console.log(err);
            })
            .then(function(updatedTrans){
                console.log('... transaction saved!');
            })
            ev.preventDefault();
        }
```

Now Reload your page, select an entry in the `PendingTransactions` list and click on a button.  

The button should become disabled, and show a busy spinner.  If you click on the [Deny] button, you can see it work.  However, the [Accept] button doesn't display the spinner.  That's because the HTML for that button doesn't include a placeholder for an icon.  Let's fix that now:
```html
<!-- [plugin]/assets/opstools/ProcessApproval/views/ProcessApproval/ProcessApproval.ejs  -->

<div class="op-container op-block nomargintop">
    <button class="btn btn-success float-right pa-approvalform-submit" pa-status="approved" ><i class="fa"></i>Accept</button>
    <button class="btn btn-info  float-right offset-15 pa-approvalform-submit mockup" pa-status="requesting" >Request</button>
    <button class="btn btn-warning float-right  offset-15 pa-approvalform-submit" pa-status="rejected"><i class="fa fa-hand-paper-o"></i>Deny</button>
</div>  
```

Reload the page now and you can see the [Accept] button working properly.

In addition to disabling the one button and showing it as busy, we also want to disable all the buttons during the network operation. 

Start by creating methods to enable and disable the buttons:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ApprovalWorkspace.js
        buttonsEnable: function() {

            for (var b in this.buttons){
                this.buttons[b].enable();
            }
        },



        buttonsDisable: function(){

            for (var b in this.buttons){
                this.buttons[b].disable();
            }
        },
```

Now disable the buttons before our Network operation:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ApprovalWorkspace.js
        '.pa-approvalform-submit click': function($el, ev) {
console.log('button clicked!');
            var _this = this;

            this.buttonsDisable();
            
            var status = $el.attr('pa-status');
            
            this.buttons[status].busy();
            
            this.transaction.attr('status', status);
            this.transaction.save()
            .fail(function(err){
                console.log(err);
            })
            .then(function(updatedTrans){
                console.log('... transaction saved!');
            })
            ev.preventDefault();
        }
```

Finally, once the Network operation is complete, we need to enable the buttons and remove the busy spinner:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ApprovalWorkspace.js
        '.pa-approvalform-submit click': function($el, ev) {
            var _this = this;

            this.buttonsDisable();
            
            var status = $el.attr('pa-status');
            
            this.buttons[status].busy();
            
            this.transaction.attr('status', status);
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
            })
            ev.preventDefault();
        }
```
>NOTE: be sure to handle both cases: `.fail()` and `.then()`

Reload the page and test the buttons.  
>NOTE: if you are working with the server locally, then the network operation happens so quickly, you probably can't actually see the disable / spinner / enable process. 

### Remove the form when the operation is complete
Once we have a valid response from the server, let's hide this interface so we know it is no longer available for updating:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ApprovalWorkspace.js
        '.pa-approvalform-submit click': function($el, ev) {
            var _this = this;

            this.buttonsDisable();
            
            var status = $el.attr('pa-status');
            
            this.buttons[status].busy();
            
            this.transaction.attr('status', status);
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
            ev.preventDefault();
        }
```

Now the form dissappears, but it is still available in the `PendingTransactions` list, and can be selected again. 

We'll update our list in the next step.


---
[< step 5 : No Comments this round](tutorial_sprint2_05_noComments.md)
[step 7 : updating the list  >](tutorial_sprint2_07_updateList.md) 