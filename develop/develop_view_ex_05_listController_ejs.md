[< Views and HTML Animation](develop_process_views.md)  
# Views and HTML Animation : Add the List Controller using EJS

In general, Mustache templates are a great way to do your templating and can handle most of your needs, especially if you don't need much logic in your actual template.  

However, when you do need to implement some additional **view** logic, EJS is a great option.   

> There is just one gotcha in our setup when implementing EJS and we'll talk about that later.


###### So back to our example:

We have our list looking like this:
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

And the data we get back from the server looks like this:
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

###### Now lets transform the `li` into our template

Read about the special [EJS Tags](http://static.javascriptmvc.com/docs/can.EJS.html).

Now, we will convert the `li` into a template:
```html
            <li class="op-container">
                <img src="<%= item.attr('iconURL') %>" class="op-avatar op-avatar-th">
                <span><%= item.attr('title') %> ( <%= item.attr('clicks') %> clicks )</span>
            </li>
```
> Note: by using the `.attr()` method, we are taking advantage of CanJS' live bindings.


###### Now let's prepare our template to process all our entries at once:
```html
            <% items.each(function(item) { %>
                <li class="op-container">
                    <img src="<%= item.attr('iconURL') %>" class="op-avatar op-avatar-th">
                    <span><%= item.attr('title') %> ( <%= item.attr('clicks') %> clicks )</span>
                </li>
            <% }) %>
```


###### Embedd the item into the `li` element, so that when I process a `li.click` event, I can reference that item.
```html
            <% items.each(function(item) { %>
                <li class="op-container" <%= (el) -> el.data( 'item', item ) %> >
                    <img src="<%= item.attr('iconURL') %>" class="op-avatar op-avatar-th">
                    <span><%= item.attr('title') %> ( <%= item.attr('clicks') %> clicks )</span>
                </li>
            <% }) %>
```

At this point, we have a valid EJS template to represent our list.  

### Now for the Gotcha! 
Unfortunately, if we keep things like this, we will run into an error when the page loads.  The reason:  our default Program controller is **ALSO** using EJS to push up it's initial view which contains this embedded view.  And our default Program controller knows nothing about the `items` for this list.

So we have to make sure our initial EJS upload doesn't interpret these commands as EJS.  And we will do that by replacing the magic tags in this inner template with another set:

| instead of        | use this           | 
| ------------- |:-------------:| 
| `<%` and `%>`     | `<!--` and `-->` | 
| `<%=` and `%>`     | `[[=` and `]]` | 
| `<%= (el) -> el.data( 'item', item ) %>`     | `obj-embed="[objName]"` | 

So now our template should look like this:
```html
            <!-- items.each(function(item) { -->
                <li class="op-container" obj-embed="[objName]" >
                    <img src="[[= item.attr('iconURL') ]]" class="op-avatar op-avatar-th">
                    <span>[[= item.attr('title') ]] ( [[= item.attr('clicks') ]] clicks )</span>
                </li>
            <!-- }) -->
```


And when our default Program controller loads, it can load this HTML without any errors.


###### At this point sync with your `mockup.html`
You are editing all these changes in your `[project]/assets/opstools/[ToolName]/views/[ToolName]/[ToolName].ejs` file.  Make sure all your changes get copied back into the shared `mockup.html` you use with your developer:

1. open `[project]/assets/opstools/[ToolName]/views/[ToolName]/[ToolName].ejs`
2. copy EVERYTHING
3. open `[project]/assets/mockup.html`
4. replace everything between the  `<!-- HTML Mockup Here --> <!-- End HTML Mockup -->` tags

Now your UI designer can still load the `mockup.html` page in any modern HTML editor and continue to work with it and change it as needed.  Only his first entry in the Mockup list now has funny `[[= item.attr('title') ]]` value.  But he'll get used to that.


###### Tell the List controller to convert this into a template
Now in your List controller, change the `initDom()` method to grab this template:
```javascript
initDOM: function () {

    // this.element.html(can.view(this.options.templateDOM, {} ));  

    // keep a reference to our list area:
    this.dom = {};
    this.dom.list = this.element.find('ul.op-list');

    var template = this.domToTemplate( this.dom.list );
}
```
> Note: we are passing in the DOM element that *contains* our template


The result is now a string that contains our template data, that has been converted to EJS:
```
<% items.each(function(item) { %>
    <li class="op-container" <%= (el) -> el.data( 'item', item ) %> >
        <img src="<%= item.attr('iconURL') %>" class="op-avatar op-avatar-th">
        <span><%= item.attr('title') %> ( <%= item.attr('clicks') %> clicks )</span>
    </li>
<% }) %>
```

And now we need to tell CanJS to make a view out of this:
```javascript
initDOM: function () {

    // this.element.html(can.view(this.options.templateDOM, {} ));  

    // keep a reference to our list area:
    this.dom = {};
    this.dom.list = this.element.find('ul.op-list');

    var template = this.domToTemplate( this.dom.list );
    can.view.ejs('[ToolName]_List', template);


    // and don't forget to clear the List area:
    this.dom.list.html('');
}
```
> NOTE: when sending the name to can.view.ejs() **DO NOT USE** "." as seperators.

If you reload the OpsPortal and view your Tool, the List should now appear empty.


###### Loading the data
Let's tell your List Controller to gather the example data from the server and populate the list.


Get the Model representing your list:

```javascript
init: function (element, options) {
    var _this = this;
    options = AD.defaults({
            templateDOM: '//opstools/ExampleTool/views/List/List.ejs'
    }, options);
    this.options = options;

    // Call parent init
    this._super(element, options);


    this.dataSource = this.options.dataSource; // AD.models.Projects;

    this.initDOM();

    this.data = {};
    this.data.list = null;
    var ItemModel = AD.Model.get('opstools.ExampleTool.List');  // <<<--- 

},
```

Now request all the items from the server and pass them to the View:
```javascript

    this.data = {};
    this.data.list = null;
    var ItemModel = AD.Model.get('opstools.ExampleTool.List');  
    ItemModel.findAll()
    .fail(function(err){
        // handle error here
    })
    .then(function(list){
        _this.data.list = list;
        _this.dom.list.html(can.view('[ToolName]_List', {items:list}));
    })

```


Reloading the page now will show you the List filled out with the results of your web service results.


###### Additional fun with the controller:  responding to clicks in the list

Let's tell the List controller to respond to clicks on the list entries.  Add a selector reference to the controller like so:
```javascript
initDOM: function () {

    // this.element.html(can.view(this.options.templateDOM, {} ));  

    // keep a reference to our list area:
    this.dom = {};
    this.dom.list = this.element.find('ul.op-list');

    var template = this.domToTemplate( this.dom.list );
    can.view.ejs('[ToolName]_List', template);


    // and don't forget to clear the List area:
    this.dom.list.html('');
},

'li click' : function($el, ev) {
    
    var item = $el.data('item');
    item.attr('clicks', item.clicks + 1);

    ev.preventDefault();
}
```

Now every time you click on an item in the list, the `( # clicks)` is automatically increments thanks to CanJS' live bindings.


###### Adding elements to the list
In our example above, we kept a reference to the list of items sent to the view ( `this.data.list`).  If you add or remove entries to this list, then the display gets updated automatically:
```javascript
var ItemModel = AD.Model.get('opstools.ExampleTool.List');

// create a new entry on the server
ItemModel.create({
    iconURL:'some/uri/hereNew.jpg',
    title:'Item New',
    clicks:0
})
.fail(function(err) {
    // handle error
})
.then(function(newItem){
    
    // now that it's been created, add it to my list
    _this.data.list.push(newItem);
})
```

Read more about CanJS'  [can.List objects](http://canjs.com/docs/can.List.html).



OK, adding any new controllers would follow the same process as adding this List Controller.  


[< Views and HTML Animation](develop_process_views.md)     
Next: [Final Reminders >](develop_view_ex_06_finalReminders.md)