[< Views and HTML Animation](develop_process_views.md)  
# Views and HTML Animation : Attach your Program Controller.

Your default program controller will be attached for you when your OpsTool loads.  

What gets displayed for your default controller is the view at: `[project]/assets/opstools/[ToolName]/views/[ToolName]/[ToolName].ejs`

This is an [Embedded Javascript (.ejs) template](http://canjs.com/docs/can.ejs.html).  


Our job now is to take the HTML provided in the `mockup.html` and move it to your `[ToolName].ejs` file.  

1. Open `mockup.html`
2. copy everything between the `<!-- HTML Mockup Here -->` and  `<!-- End HTML Mockup -->` tags.
3. Open `[ToolName].ejs`
4. Replace everything with what you copied.


For this example, your `[ToolName].ejs` should now look like:
```html
<div class="op-stage-body">

	<!-- List Widget -->
    <div class="col-xs-2 op-container">
       <!-- contents removed for brevity -->
    </div>

    <!-- Detail Widget -->
    <div class="col-xs-10 op-container">
        <!-- contents removed for brevity -->
    </div>

</div>
``` 

Now refresh your OpsPortal in the web browser, and your new tool should look like the Mockup.html draft that was created.



[< Views and HTML Animation](develop_process_views.md)     
Next: [Understand The Program Controller >](develop_view_ex_04_understandProjectController.md)