[< Views and HTML Animation](develop_process_views.md)  
# Views and HTML Animation : Add the List Controller

In order to add the List controller, we need to:


###### Add it to the Program Controller's dependencies
Our default Program controller needs to make sure it is loaded before we add it:

```javascript
steal(
        // List your Controller's dependencies here:
        'appdev',
        '//OpsPortal/classes/OpsTool.js',
        '/opstools/[ToolName]/controllers/List.js',   // <<----- 
        '/opstools/[ToolName]/views/[ToolName]/[ToolName].ejs',
function(){

```

###### Now in our init() lets keep track of all the controllers we manage:
```javascript
init: function (element, options) {
    var self = this;
    options = AD.defaults({
            templateDOM: '//opstools/[ToolName]/views/[ToolName]/[ToolName].ejs',
            resize_notification: '[ToolName].resize'
    }, options);
    this.options = options;

    // Call parent init
    this._super(element, options);

    this.controllers = {};  // <<-----

    this.initDOM();

},
```


###### Add a method to create all our controllers AFTER the DOM is created:
```javascript
init: function (element, options) {
    var self = this;
    options = AD.defaults({
            templateDOM: '//opstools/[ToolName]/views/[ToolName]/[ToolName].ejs',
            resize_notification: '[ToolName].resize'
    }, options);
    this.options = options;

    // Call parent init
    this._super(element, options);

    this.controllers = {};  

    this.initDOM();

    this.initControllers(); // <<-----
},


initControllers:function() {
    

}
```


Now open your `[project]/assets/opstools/[ToolName]/controllers/List.js` file and look how it is defined:
```javascript
AD.Control.extend('opstools.[ToolName].List', {  
   // stuff here
});
```


In our `initControllers()` method we need to get a reference to our controller:
```javascript
initControllers:function() {
    
    var List = AD.Control.get('opstools.[ToolName].List');

}
```

Now in order to attach the List to the current DOM of our Project, we need to identify the HTML element for this controller.  Unfortunately, the current HTML doesn't have a unique marker for this controller.  So lets add one.  Open your `[project]/assets/opstools/[ToolName]/views/[ToolName]/[ToolName].ejs` and add a unique `.[toolname]-list` class to it like so:
```html
<!-- List Widget -->
<div class="col-xs-2 op-container op-widget [toolname]-list">
   <!-- contents removed for brevity -->
</div>

```
> NOTE: replace [toolname] with whatever you named this OpsPortal tool.



Now our `initControllers()` can attach the List like:
```javascript
initControllers:function() {
    
    var List = AD.Control.get('opstools.[ToolName].List');
    this.controllers.List = new List( this.element.find('.[toolname]-list'), {} );

}
```


Now if you refresh the OpsPortal and look at the page, you will now see a **mess** where your list once was.  Why?  Well the default behavior of the List controller is going to want to display it's view located at: `[project]/assets/opstools/[ToolName]/views/List/List.ejs`  

Remember, that is not the approach we are taking during our development sprints, so we need to tell the List controller not to do that.  Open the List controller and edit it's `initDom()` method like so:
```javascript
initDOM: function () {

    // this.element.html(can.view(this.options.templateDOM, {} ));  // <<<--- 

}
```
> While your at it, you can remove any other references to that view, like in the `steal()` list.

Now when you refresh the OpsPortal and look at your Tool, the page displays like you expected.  So on to the fun part:



###### Making a template out of the existing HTML
Lets take a look at the html where your List controller is attached now:
```html
<!-- List Widget -->
<div class="col-xs-2 op-container op-widget [toolname]-list">
   <div class="op-widget-masthead">
        <h3>List Title</h3>
        <p>This is a great description of this list.</p>
    </div>
    <div class="op-widget-body">
        <ul class="op-list">
            <li class="op-container">
                <img src="/images/avatar/item1.jpg" class="op-avatar op-avatar-th">
                <span>Item 1 ( 0 clicks )</span>
            </li>
            <li class="op-container active">
                <!-- content removed for brevity, example entry 2 -->
            </li>
            <li class="op-container">
                <!-- content removed for brevity, example entry 3 -->
            </li>
            <li class="op-container">
                <!-- content removed for brevity, example entry 4 -->
            </li>
        </ul>
    </div>
    <div class="op-widget-footer">
        <a class="opsportal-modal tt" href="#" title="Add List Item"><span class="icon"><i class="fa fa-plus fa-fw"></i></span></a>
    </div>
</div>
```

Now, I'm expecting my List controller to get a list of entries from the server, and display them in this list.  

The data we get back from the server will look like:
```json
[
    { 
        iconURL:'some/uri/here.jpg',
        title:'Item 1',
        clicks:0
    },
    {
        iconURL:'some/uri/here2.jpg',
        title:'Item 2',
        clicks:0
    },
    ...
    {
        iconURL:'some/uri/hereN.jpg',
        title:'Item N',
        clicks:0
    }
]
```

The `ul.op-list` element will then contain a `li` entry for each of the entries returned from the server.  So we are going to transform the inside of our `ul.op-list` element into our template.

+ start by marking all the other `li` entries as `.mockup`
> Note: I chose one of the li entries that was not marked `.active`
```html
        <ul class="op-list">
            <li class="op-container">
                <img src="/images/avatar/item1.jpg" class="op-avatar op-avatar-th">
                <span>Item 1 ( 0 clicks )</span>
            </li>
            <li class="op-container active mockup">
                <!-- content removed for brevity, example entry 2 -->
            </li>
            <li class="op-container mockup">
                <!-- content removed for brevity, example entry 3 -->
            </li>
            <li class="op-container mockup">
                <!-- content removed for brevity, example entry 4 -->
            </li>
        </ul>
```

Now our tools for creating a template out of the HTML will ignore (and remove) those entries.  The one that is left is the one we will transform into our template.

At this point, I'm going to show you two different ways to implement the template:

+ [using Embedded Javascript (.ejs)](develop_view_ex_05_listController_ejs.md)
+ using Mustache 




[< Views and HTML Animation](develop_process_views.md)     
Next: [using Embedded Javascript (EJS) >](develop_view_ex_05_listController_ejs.md)