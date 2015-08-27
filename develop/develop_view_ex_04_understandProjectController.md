[< Views and HTML Animation](develop_process_views.md)  
# Views and HTML Animation : Understand the Project Controller.

Let's analyze how your default Program Controller works.

To begin with, open up your default Program Controller in `[project]/assets/opstools/[ToolName]/controllers/[ToolName].ejs`.

It should look like this (without comments) :
```javascript
steal(
        // List your Controller's dependencies here:
        'appdev',
        '//OpsPortal/classes/OpsTool.js',
        '/opstools/[ToolName]/views/[ToolName]/[ToolName].ejs',
function(){

    AD.Control.OpsTool.extend('[ToolName]', {

        init: function (element, options) {
            var self = this;
            options = AD.defaults({
                    templateDOM: '//opstools/[ToolName]/views/[ToolName]/[ToolName].ejs',
                    resize_notification: '[ToolName].resize'
            }, options);
            this.options = options;

            // Call parent init
            this._super(element, options);

            this.initDOM();
        },

        initDOM: function () {

            this.element.html(can.view(this.options.templateDOM, {} ));

        }
    });

});
```


###### Dependencies
The first part of the file specifies the dependencies required before this code can be run.  In this case we are using JavascriptMVC's [steal](http://static.javascriptmvc.com/docs/stealjs.html) library to specify our dependencies.

```javascript
steal(
        // List your Controller's dependencies here:
        'appdev',
        '//OpsPortal/classes/OpsTool.js',
        '/opstools/[ToolName]/views/[ToolName]/[ToolName].ejs',
function(){
```
Currently this controller depends on our

+ appdev library
+ the OpsTool.js class defined in our OpsPortal
+ and the default view [ToolName].ejs

> Note: all the paths are specified from the `[sails]/assets` directory.

StealJS will load all those packages before running the fn() that contains our Controller.


###### Controller Creation
Our `appdev` library provides an API for creating new OpsTools:
```javascript 
AD.Control.OpsTool.extend( [ControllerName], { Controller Definition } );
```


in our `{ Controller Definition }` we defined two methods:
 
+ init(): called by CanJS objects when they are created
+ initDom(): called at the end of init() to setup our initial view.

our `initDom()` actually pushes our HTML view to the DOM:

+ `this.element.html()` : `this` controller is attached to a DOM `element`, and we are setting the `html()` of that element to the data we send the function:  which is from
+ `can.view( 'templateURL', {data})` 





[< Views and HTML Animation](develop_process_views.md)     
Next: [Add the List Controller >](develop_view_ex_05_listController.md)