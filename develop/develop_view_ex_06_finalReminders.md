[< Views and HTML Animation](develop_process_views.md)  
# Views and HTML Animation : Final Reminders

Keep in mind that our goal is to 

1. have a common reference document between our UI Designer and our UI Programmers: `mockup.html`
2. reduce the effort in incorporating changes in the UI design into our MVC architecture.


###### UI Designer
The UI Designer is able to use their HTML editor of choice to work with the `mockup.html` file in the project.  

Each sprint, as they merge changes between their work and the changes the UI Programmers are making, pay attention to see what referece class ids are being created, and try to keep them in their proper places when modifying the HTML design.

The UI Designer should also embedded tags for the templates in their respective places and work around them for new changes to the design.


###### UI Programmer
The UI Programmer's challenge is to keep the changes he is implementing in the main controller's view in sync with the `mockup.html` file.  

At the beginning of each sprint, pull down any changes to the `mockup.html` file and do any merging with your local changes there.  Once all the conflicts are resolved, copy those changes back to the main controller's view file and work on your next feature.

When you are finishing off a feature make sure all changes are copied back to the `mockup.html` file before committing.  


This is process we use on our team.



[< Views and HTML Animation](develop_process_views.md)     