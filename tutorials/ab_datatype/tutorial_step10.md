[< Tutorial: Fix Width And Height](tutorial_step9.md)
# Tutorial - Custom Editor

Now one more enhancement to make.  When looking at the images on the grid, if a user clicks on an image we want it to function like a typical file upload component that allows you to browse your file system and select a file.

However, we used our own display for the image and our `webix.uploader` was set to `apiOnly:true`.  So we will have to manually tell the uploader to open the `.fileDialog()`


### Add the customEdit method
For Data Fields that operate like normal webix data types, you can leave the `.customEdit` method commented out.  However, if your Data Field requires a special action to edit it (like using a popup with additional info, or triggering another event, etc...) then you should uncomment the `.customEdit` method and fill it in:

```javascript
// [ABRootUI]/controllers/data_fields/image.js

imageDataField.customEdit = function (application, object, fieldData, rowId, itemNode) {
	if (!application || !object || !fieldData  || !rowId) return false;

	return false;
};
```

This default definition will simply prevent any action by the webix grid.

First, let's see if we can build a reference to the hidden uploader created in our `.customDisplay`:

- create a new method to create the unique reference for the uploader:
```javascript
imageDataField.getContainer = function (itemNode) {
	var $container = $(itemNode).data('image-container');

	if (!$container) $container = $(itemNode).find('.ab-image-data-field');

	return $container;
}
imageDataField.keyField = function (application, object, fieldData, rowId) {
	return [ application.name, object.name, fieldData.name, rowId, AD.util.uuid()].join('-');
	// note: the AD.util.uuid() ensures this is a unique entry
}
imageDataField.keyContainer = function (itemNode) {
	var $container = this.getContainer(itemNode);
	return [ $container.attr('id'), 'container' ].join('-');
}
imageDataField.keyUploader = function (itemNode) {
	var $container = this.getContainer(itemNode);
	return [ $container.attr('id'), 'uploader' ].join('-');
}
```

- reference the uploader using the new shared method
```javascript

imageDataField.customEdit = function (application, object, fieldData, rowId, itemNode) {
	if (!application || !object || !fieldData ) return false;

	var keyUploader = this.keyUploader(itemNode);

	return false;
};
```

- now make sure our original `.customDisplay` uses that same method for the uploader's reference:
```javascript

	imageDataField.customDisplay = function (application, object, fieldData, rowId, data, itemNode, options) {

	
		var keyField = this.keyField( application, object, fieldData, rowId);

		////
		//// Prepare the Display
		////

		// find the container from our this.getSettings().setting.template 
		var $container = $(itemNode).find('.ab-image-data-field');

		// clear contents
		$container.html('');
		$container.attr('id', keyField);				// <-- after this we can use .keyContainer() and .keyUploader()

		var keyContainer = this.keyContainer(itemNode); // keyField+'-container';
		var keyUploader = this.keyUploader(itemNode);  // keyField+'-uploader';

```


Now that we are sure we can reference the uploader for the instance the user has clicked on, now tell it to Open it's File Dialog:

```javascript
imageDataField.customEdit = function (application, object, fieldData, rowId, itemNode) {
	if (!application || !object || !fieldData ) return false;

	var keyUploader = this.keyUploader(itemNode);
	$$(keyUploader).fileDialog({ rowid : rowId });

	return false;
};
```


That's it!  Now reload the App Builder and click on one of the images in the column.  You should be able to select a new image to replace it.


### Form Handling


##### Form .save()
When a form wants to access the data from your Data Field, it will call a `.getValue()` method.

When our image data field is on a form, we need to return the image `uuid`.  

+ first modify our template to save the `uuid` as an attribute of the `<div>`:
```javascript
// in .customDisplay() :

$container.showIcon = function () {
	$container.find('.image-data-field-image').attr('image-uuid', '' );    // <-- clear the uuid
	$container.find('.image-data-field-image').hide();
	$container.find('.image-data-field-icon').show();
}
$container.showImage = function (uuid) {
	$container.find('.image-data-field-image').css('background-image', "url('/opsportal/image/"+application.name+'/'+uuid+"')" );
	$container.find('.image-data-field-image').attr('image-uuid', uuid );  // <-- save the uuid
	$container.find('.image-data-field-icon').hide();
	$container.find('.image-data-field-image').show();
}

```

+ Now implement the `.getValue()` method:
```javascript
imageDataField.getValue = function (application, object, fieldData, itemNode) {
	var image = $(itemNode).find('.image-data-field-image');

	return image.attr('image-uuid');
};
```


##### Form .show()
When a form displays your new Data Field, it will use the `.setValue()` method to show you what to display.

Tell the image data field how to update itself with a new value:
```javascript
	imageDataField.setValue = function (fieldData, itemNode, data) {
		var $container = this.getContainer(itemNode);

		if ( !data || data == '') {
			$container.showIcon();
		} else {
			// else display the image:
			$container.showImage(data);
		}

	};
```



There, we have covered all the basics for your new Image Data Field.


---
[< Step 9 : Fix Width And Height](tutorial_step9.md)
[Summary  >](tutorial_summary.md) 

<!-- 
The `.customEdit` method works fine for the grid component, but not for a Form.  We'll have to modify this to work in both cases.

+ first: lets modify the `.customEdit` to simply stop the normal execution:
```javascript
imageDataField.customEdit = function (application, object, fieldData, rowId, itemNode) {
	if (!application || !object || !fieldData  || !rowId) return false;


	// var keyUploader = this.keyUploader(application, object, fieldData, rowId);
	// $$(keyUploader).fileDialog({ rowid : rowId });


	return false;
};
```

+ now: modify the `.customDisplay` so that the container that holds our icon and image responds to the clicks:
```javascript
// in .customDisplay():

// use a webix component for displaying the content.
// do this so I can use the progress spinner
var webixContainer = webix.ui({
	view:'template',
	id: keyContainer,
	container:keyField,
	
	template:imgDiv,

	borderless:true,
	height: imgHeight,
	width:imgWidth,
	onClick:{

		'image-data-field-icon': function(ev, id) {				// <- - click on the icon
			$$(keyUploader).fileDialog({ rowid : id });				
		},

		'image-data-field-image': function(ev, id) {			// <- - click on the image
			$$(keyUploader).fileDialog({ rowid : id });
		}
		
	}
});
webix.extend(webixContainer, webix.ProgressBar);
```

This should work for both Forms and Grids. 

-->