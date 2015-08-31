# Sprint 0 : Step 4: Outsource our UI Design 
Now we are at a good point to outsource our UI design.

This would be a good time to read about our [approach to handling views](../develop/develop_process_views.md) in our development process.

Currently this plugin has a set of mockup files :

+ `/assets/mockup.html` : Contains the HTML mockup of the project
+ `/assets/mockup_setup.js` :  Contains any javascript code the UI designer needs to animate his mockup

At this point in the Sprint, I now send my UI developer the project design document, and tell him to prepare [their own copy](../develop/develop_contribute.md) of the project:

+ [setup a development environment](../develop/develop_setup.md)
+ git clone [THEIR Fork](../develop/develop_contribute_fork.md) of the project
   ```sh
   # assuming in sails directory:
   $ cd node_modules
   $ git clone https://github.com/[DeveloperAccount]/opstool-process-approval.git
   $ cd opstool-process-approval/
   $ git checkout -b develop
   $ git push -u origin develop
   ```

+ and pull down a clone of the appdev-mockups project to make sure these html templates work.
   ```sh 
   $ cd assets
   $ git clone https://github.com/appdevdesigns/appdev-mockups.git mockups
      # pay attention to the "mockups" folder name
   ```

+ Now open the `assets/mockup.html` in a browser and verify there are no errors.
> But not Chrome!  They prevent local file access.  






[< sprint 0](tutorial_sprint0.md)
[step 5 : Some Initial UI Tests >](tutorial_sprint0_04_initialUITests.md) 
