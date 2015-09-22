[< Views and HTML Animation](develop_process_views.md)  
# Views and HTML Animation : Develop a mockup

In your project directory, there is an `assets/mockup.html` file.  This file is for your UI developer to craft the HTML UI for the project.  Inside this file there is a section for your developer to place his HTML in:
```html
<!-- Stage -->  
<div class="op-container op-stage">

      <div class="opsportal-area-ViewExample" style="display: block;">
          <div class="opsportal-area-tool-ViewExample">
            <div class="op-container op-stage-container" id="">


<!-- HTML Mockup Here -->


                <!-- if your design uses a masthead on the stage, use this: -->
                <!-- if not ... delete this! -->
                <div class="op-container op-stage-masthead">
                  <h2>Did ya need a masthead? </h2>
                </div>

                <div class="op-stage-body">

                    <!-- The rest of your design should fit inside this div.op-stage-body -->
                    <h1> ViewExample Mockup </h1>
                    <p> place all your HTML code here... </p>


                </div>


<!-- End HTML Mockup -->

            </div>
          </div>
      </div>

</div>
```


### Important:
+ your UI designer must keep all added HTML with in the `<!-- HTML Mockup Here --> <!-- End HTML Mockup -->` tags.
+ If your UI designer needs to add any other resources (javascript widget, fonts, etc..) they should be stored in the related `[project]/assets` directories.
+ if the UI designer needs to add any javascript to illustrate the functioning of the mockup, add that to the `[project]/assets/mockup_setup.js`


### When they are done:
You designer updates `[project]/assets/mockup.html` and pushes their changes back to the project.  Now it looks like:
```html
<div class="op-container op-stage">

      <div class="opsportal-area-ViewExample" style="display: block;">
          <div class="opsportal-area-tool-ViewExample">
            <div class="op-container op-stage-container" id="">


<!-- HTML Mockup Here -->


                <div class="op-stage-body">

                	  <!-- List Widget -->
                    <div class="col-xs-2 op-container op-widget ">
                       <!-- contents removed for brevity -->
                    </div>

                    <!-- Detail Widget -->
                    <div class="col-xs-10 op-container">
                        <!-- contents removed for brevity -->
                    </div>

                </div>


<!-- End HTML Mockup -->

            </div>
          </div>
      </div>

</div>
```

[< Views and HTML Animation](develop_process_views.md)     
Next: [plan your UI Controllers >](develop_view_ex_02_planControllers.md)