[< Tutorial: Something Went Wrong](tutorial_step8.md)
# Tutorial - Fix Width And Height

At this point, if you drag images onto your fields, the images are too big to fit into the grid cell the image is defined in.  
![imagesTooSmall](images/step9_imagesTooSmall.png "Images Too Small")


### Add Some New Settings
So, let's go and allow the user to specify a `width` and `height` parameter for this image when displaying in a grid or form.

- Add some additional webix form components in the `.editDefinition` settings:
```javascript
// [ABRootUI]/controllers/data_fields/image.js

	imageDataField.editDefinition = {
		id: componentIds.editView,
		rows:[
			{
				cols: [
					{
						view:"checkbox", 
						id:"useWidth", 
						labelRight:"width", 
			            width: 80,
			            labelWidth: 0,
						value:0,
			            click: function() {
							if (this.getValue())
								$$('imageWidth').enable()
							else
								$$('imageWidth').disable();
			            }
					},
					{
						view: 'text',
			          	id: 'imageWidth'
					}
				]
			},
			{
				cols: [
					{
						view:"checkbox", 
						id:"useHeight", 
						labelRight:"height", 
			          	width: 80,
			            labelWidth: 0,
						value:0,
			            click: function() {
							if (this.getValue())
								$$('imageHeight').enable()
							else
								$$('imageHeight').disable();
			            }

					},
					{
						view: 'text',
			          	id: 'imageHeight'
					}
				]
			}
		]
	}
```

- `useWidth` and `useHeight` checkboxes will indicate if we should pay attention to the respective `width` and `height` settings when displaying our image.
- `imageWidth` and `imageHeight` are the actual settings if the checkboxes are clicked

Reload your Ops Portal, choose your `Cast` object and then edit the `Image` column.
![editColumn](images/step9_editColumn.png "Edit Column")


And now you can see our additional settings:
![newEditValues](images/step9_newEditValues.png "New Edit Values")


### Save those Settings
Now let's make sure those settings are saved and stored in our instance's setting values:
```javascript
	imageDataField.getSettings = function () {

		return {
			fieldName: imageDataField.name,
			type: imageDataField.type,
			setting: {
				icon: imageDataField.icon,

				editor: 'imageDataField',
				template:'<div class="ab-image-data-field"></div>',

				filter_type: 'text', 

				useWidth	: $$('useWidth').getValue(),			//<--- add values to our return obj
				imageWidth	: $$('imageWidth').getValue(),
				useHeight	: $$('useHeight').getValue(),
				imageHeight	: $$('imageHeight').getValue()
			}
		};
```

Reload your Ops Portal, and then edit your column again.  Now set some values:
![setSomeValues](images/step9_setSomeValues.png "Set Some Values")

Click `[Save]`.  

Looks good so far.  Now click `[Add new Column]` and select our `Image` Data Field again:
![notReset](images/step9_notReset.png "Not Reset")

Notice how the form fields have not been reset for a new entry?


### Reset the Form Values
Let's now reset those values:
```javascript
imageDataField.resetState = function () {

	$$('useWidth').setValue(0);
	$$('imageWidth').setValue('');
	$$('useHeight').setValue(0);
	$$('imageHeight').setValue('');
	
};
```


Now run through those steps again:

- reload the Ops Portal
- edit the existing Column info: add width 50, height 75
- save 
- click `[Add New Column]`
- choose our `Image` data field
- see that the values are now cleared out.


Now, did you notice back on the second step above, that the editing of the existing column info did not have the default values added?  


### Populate From Settings
Lets tell our Data Field to default it's stored values on an edit operation.  

**But Wait:** I'm getting nervous with all the repeated strings I'm using for the Webix ids for our form components.  So first let's create those strings in our `componentIds` and reuse those in our definitions:
```javascript
var componentIds = {
	editView: 'ab-new-image',

	useWidth	: 'useWidth',
	imageWidth	: 'imageWidth', 
	useHeight	: 'useHeight',
	imageHeight	: 'imageHeight'
};
```
`<taking a deep breath>`_OK, I'm feeling better about this now._`</taking a deep breath>` Let's continue:

```javascript
imageDataField.populateSettings = function (application, data) {
	if (!data.setting) return;

	$$(componentIds.useWidth).setValue(data.setting.useWidth);
	$$(componentIds.imageWidth).setValue(data.setting.imageWidth);
	$$(componentIds.useHeight).setValue(data.setting.useHeight);
	$$(componentIds.imageHeight).setValue(data.setting.imageHeight);

};
```

Now, try this:

- Reload Ops Portal and view your `Cast` Object
- Edit our column information:

You should now see the previous settings implemented in the popup editor.
![populated](images/step9_setSomeValues.png "Values Populated")



### Change the view of the Image
Now we are going to actually effect the display of the image in the grid.  First, let's put a `width` and `height` style on the `img` element we are displaying:
```javascript
// in the .customDisplay() method:

		// clear contents
		$container.html('');
		$container.attr('id', keyField);


		var style = '';
		if (fieldData.setting.useWidth) {
			style = 'width:'+fieldData.setting.imageWidth+'px;';
			fieldData.setting.width = fieldData.setting.imageWidth;
		}
		if (fieldData.setting.useHeight) {
			style += 'height:'+fieldData.setting.imageHeight+'px;';
		}
		if (style != '') {
			style = 'style="'+style+'"';
		}

		// the display of our image:
		// .image-data-field-icon : for an image icon when no data is present
		// .image-data-field-image: for an actual <img> of the data.
		var imgDiv = [
			'<div class="image-data-field-icon" style="text-align: center;display:none;"><i class="fa fa-file-image-o fa-2x"></i></div>',
			'<div class="image-data-field-image" style="display:none;"><img src="" '+style+' ></div>'
		].join('\n');


		// use a webix component for displaying the content.
		// do this so I can use the progress spinner
		var webixContainer = webix.ui({
			view:'template',
```


Then tell the Data Field to return a unique `.getRowHeight` value.  There is a commented out `getRowHeight` method to uncomment and use:
```javascript
	imageDataField.getRowHeight = function (fieldData, data) {
		
		var height = 36;
		if (fieldData.setting.useHeight) {
			height = parseInt(fieldData.setting.imageHeight);
		}
		return height;
	};
```
>Note: it is important to return an `integer` here and not a `string`


Now let's reload and see the display:
![soClose](images/step9_soClose.png "So Close")


I forgot about the webix component that wraps our `img` element for showing the progress spinner:
```javascript

		// the display of our image:
		// .image-data-field-icon : for an image icon when no data is present
		// .image-data-field-image: for an actual <img> of the data.
		var imgDiv = [
			'<div class="image-data-field-icon" style="text-align: center;display:none;"><i class="fa fa-file-image-o fa-2x"></i></div>',
			'<div class="image-data-field-image" style="display:none;"><img src="" '+style+' ></div>'
		].join('\n');



		var imgHeight = 33;										// <-- calculate the height
		if (fieldData.setting.useHeight){
			imgHeight = parseInt(fieldData.setting.imageHeight);
		}

		var imgWidth = 50;										// <-- and width
		if (fieldData.setting.useWidth){
			imgWidth = parseInt(fieldData.setting.imageWidth);
		}

		// use a webix component for displaying the content.
		// do this so I can use the progress spinner
		var webixContainer = webix.ui({
			view:'template',

			container:keyField,
			
			template:imgDiv,

			borderless:true,
			height: imgHeight,									// <-- use them here
			width:imgWidth
		});
		webix.extend(webixContainer, webix.ProgressBar);


		$container.showIcon = function () {
			// $($container.find('img')).prop('src', '');
			$container.find('.image-data-field-image').hide();
			$container.find('.image-data-field-icon').show();
		}

```

Now reload and see the display:
![stillNotQuite](images/step9_stillNotQuite.png "Still Not Quite")


Closer.  The image element itself is properly sized, but the Webix grid cell's width isn't.  So let's tell Webix to use our desired width.  Do that by returning a `width` property in our `.settings` value:
```javascript
imageDataField.getSettings = function () {

	var setting = {
			icon: imageDataField.icon,

			editor: 'imageDataField',
			template:'<div class="ab-image-data-field"></div>',

			filter_type: 'text', // DataTableFilterPopup - filter type

			useWidth	: $$(componentIds.useWidth).getValue(),	
			imageWidth	: $$(componentIds.imageWidth).getValue(),		
			useHeight	: $$(componentIds.useHeight).getValue(),
			imageHeight	: $$(componentIds.imageHeight).getValue()
		}

	if ($$(componentIds.useWidth).getValue()) { 
		setting.width = $$(componentIds.imageWidth).getValue();
	}

	return {
		fieldName: imageDataField.name,
		type: imageDataField.type,
		setting: setting
	};
};
```
Reload the Ops Portal.  And you will have to `Edit` the column and resubmit the data for these new values to be active in the display. 
![shoot](images/step9_shoot.png "Shoot!")


So close ... Webix grid cells have a padding for their contents by default.

To fix this, let's create a css style that removes padding for an embedded div:
```css
/* in [ABRootUI]/BuildApp.css */

/* No padding/margin column */
.ab-column-no-padding div {
	padding: 0px !important;
	margin: 0px !important;
}
```

Now when we return our `.getSettings()` make sure this css style is applied to our column:
```javascript

			useWidth	: $$(componentIds.useWidth).getValue(),	
			imageWidth	: $$(componentIds.imageWidth).getValue(),		
			useHeight	: $$(componentIds.useHeight).getValue(),
			imageHeight	: $$(componentIds.imageHeight).getValue()
		}

	if ($$(componentIds.useWidth).getValue()) { 
		setting.width = $$(componentIds.imageWidth).getValue();
		setting.css = 'ab-column-no-padding';						// <--- add css style
	}

	return {
		fieldName: imageDataField.name,
		type: imageDataField.type,
		setting: setting
	};
```

In addition, I decided that instead of using an `<img />` element, I wanted to show the images as a `background-image` in the embedded `<div>`:

```javascript
// in .customDisplay():

// the display of our image:
// .image-data-field-icon : for an image icon when no data is present
// .image-data-field-image: for an actual <img> of the data.
var imgDiv = [
	'<div class="image-data-field-icon" style="text-align: center;display:none;"><i class="fa fa-file-image-o fa-2x"></i></div>',
	'<div class="image-data-field-image" style="display:none; width:100%; height:100%; background-repeat: no-repeat; background-position: center center; background-size: cover;"></div>'
].join('\n');
```

And to change how we update the image:
```javascript
$container.showImage = function (uuid) {
	$container.find('.image-data-field-image').css('background-image', "url('/opsportal/image/"+application.name+'/'+uuid+"')" );
	$container.find('.image-data-field-icon').hide();
	$container.find('.image-data-field-image').show();
}
```

Now when we reload the page and display:
![there](images/step9_there.png "There!")


---
[< Step 8 : Something Went Wrong](tutorial_step8.md)
[Step 10 : Custom Editor >](tutorial_step10.md) 