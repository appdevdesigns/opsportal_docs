[< Tutorial Sprint 2](tutorial_sprint2.md)
# Tutorial - Catch Template Errors

### Overview
By now you have gotten tired of that fixture entry giving us errors in the display because of a missing template reference.  In this step, we are going to catch that error and handle it properly.


### Analyzing the Error:
When you click on the 3rd sample entry in our Fixture data, you are provided with a reference to a template that doesn't exist.  You should see an error message popping up in your console:
```
[Error] Failed to load resource: the server responded with a status of 404 (Not Found) (notThere.ejs, line 0)
[Error] Error: can.view: No template or empty template:http://localhost:1337/opstools/ProcessApproval/views/notThere.ejs
```

Depending on your browser, you might also be able to click on the second entry and see a stack trace show up:
```
[Error] Failed to load resource: the server responded with a status of 404 (Not Found) (notThere.ejs, line 0)
[Error] Error: can.view: No template or empty template:http://localhost:1337/opstools/ProcessApproval/views/notThere.ejs
    ajax
    getRenderer
    (anonymous function)
    renderAs
    template
    embeddTemplate
    setTransaction
    (anonymous function)
    dispatch
    handle
    trigger
    (anonymous function)
    each
    each
    trigger
    selectLI
    li click
    (anonymous function)
    dispatch
    handle
```

If you follow that stack tract from the top and work your way down, the first method we come to that **we** created is `.embeddTemplate()`.

This is happening in the `ApprovalWorkspace.embeddTemplate()` method:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ApprovalWorkspace.js
        embeddTemplate: function(sel, templateInfo) {

            // compile template data
            var data = templateInfo.viewData || {};
            if (templateInfo.data) {
                data.data = templateInfo.data;
            }

            var $el = this.element.find(sel);

            $el.html( can.view(templateInfo.view, data) );
        },
```

It turns out that the `can.view()` method is actually **throw**-ing an error when it can't find the specified template.  Apparently CanJS feels this is a serious enough of an error to `throw new Error()`, and the only way we can handle this is to `try ... catch()` this code:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ApprovalWorkspace.js
        embeddTemplate: function(sel, templateInfo) {


            // compile template data
            var data = templateInfo.viewData || {};
            if (templateInfo.data) {
                data.data = templateInfo.data;
            }

            var $el = this.element.find(sel);

            try {

                $el.html( can.view(templateInfo.view, data) );

            } catch(e) {

                // respond to the error here
            }

        },
```
 
We should at least try to alert our Developers that there is a developer level error going on:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ApprovalWorkspace.js
            } catch(e) {

                // This is most likely a template reference error.
                AD.error.log('Error displaying template:'+templateInfo.view, { error:e });

            }
```
> NOTE: as always when alerting the developers, ask yourself what info you might need to debug this issue and include that in your data.


But in this case, I decide that maybe we can dump enough of the base info out that maybe an administrator could look at it and still make a decision:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ApprovalWorkspace.js
            } catch(e) {

                // This is most likely a template reference error.
                AD.error.log('Error displaying template:'+templateInfo.view, { error:e });

                var displayData = data.data || data;
                var errorDisplay = [ 
                    'Error displaying provided object template ('+templateInfo.view+')',
                    'Here is the raw data:'
                ];

                for(var d in displayData.attr()) {
                    errorDisplay.push(d+' : '+ displayData[d]);
                }
                   
                $el.html(errorDisplay.join('<br>'));
            }
```

Now Reload the OpsPortal and click on that troublesome entry.  That is not pretty!   But you should be able to [Accept] or [Deny] it now.

Also, when you look at the console messages again, you'll see our new `Error displaying template: ...` message.  If the communications between your UI and server are operating, then a message should have been recorded in the developer's logs on the server.


Oh, and one more thing ...

Since this can display images that have been uploaded from multiple sources, we need to handle special cases.  Apparently some smart phones will take an image in portrait mode, but actually store it in horizontal format and add extra EXIF data to indicate the image should be rotated.  

HTML <img> tags don't actually look at the EXIF data and will simply display the image as it is physically stored ... horizontally.

We have an OpPortal widget to handle rotating images like this, so let's add that to the template:
```javascript
// [plugin]/assets/opstools/ProcessApproval/controllers/ApprovalWorkspace.js

            try {

                $el.html( can.view(templateInfo.view, data) );

                // attach any embedded op images
                $el.find('[ap-op-image]').each(function(i, el){
                    new AD.op.Image(el);
                });

            } catch(e) {

                // respond to the error here
            }
```

For a template to display an image that needs to be rotated, it needs a <div> defined with an attribute *ap-op-image* like so:
```html
<div class="img-responsive" ap-op-image="true" opimage-url="<%= data.image %>">
</div>
```
the *opimage-url* attribute will auto load the image and have it rotated.

> NOTE: we use 'ap' instead of 'ad' because many Advertisement blockers will prevent this element.

---
[< step 7 : Updating the List](tutorial_sprint2_07_updateList.md)
[step 9 : Real Time Updates  >](tutorial_sprint2_09_realTimeUpdates.md) 