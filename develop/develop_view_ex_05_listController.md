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
<div class="col-xs-2 op-container [toolname]-list">
   <!-- contents removed for brevity -->
</div>

```


Now our `initControllers()` can attach the List like:
```javascript
initControllers:function() {
    
    var List = AD.Control.get('opstools.[ToolName].List');
    this.controllers.List = new List( this.element.find('.[toolname]-list'), {} );

}



Now if you refresh the OpsPortal and look at the page, you 

////// LEFT OFF HERE




[< Views and HTML Animation](develop_process_views.md)     
Next: [Add the List Controller >](develop_view_ex_05_listController.md)