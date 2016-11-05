[< Tutorial: Display The image uploader widget](tutorial_step7.md)
# Tutorial - Something Went Wrong


### TODO

for now the short of it is:  

- the Webix uploader is incompatible with our service
- it only works for small files that can all be transferred at once.
- larger files will report with a missing parameters error


### To Fix:
Simply change to a different url:
```javascript

		// The Server Side action key format for this Application:
		var actionKey = 'opstool.AB_'+application.name.replace('_','')+'.view';
		var url = '/'+[ 'opsportal', 'image', application.name, actionKey, '1'].join('/');

		var uploader = webix.ui({ 
		    view:"uploader",  
		    id:keyUploader, 
		    apiOnly: true, 
		    upload:url,							// <--- the values are encoded in the url
		    inputName:'image',
		    multiple: false,
		    // formData:{						// <--- we don't use this anymore 
		    // 	appKey:application.name,
		    // 	permission:actionKey,
		    // 	isWebix:true
		    // },
		    on: {

```


At some point, I'll step you through how I discovered this error and how I implemented the fix.

Until then ... move along ...



---
[< Step 7 : Display The image uploader widget](tutorial_step7.md)
[Step 9 : Fix Width And Height >](tutorial_step9.md) 