# Sprint 0 : Step 5: My Full Fixture Data:

This is the full fixture data set to copy into `[plugin]/test/fixtures/approvalrequests.json` 

```json
 [
   {
     "actionKey":"process.approval.tool.view",
     "userID":"user.1",
     "callback":"return.message.queue.1",
     "status":"pending",
     "objectData":{
       "menu":{
         "icon":".fa-calendar",
         "action": {
           "key":"[newActivity]",
           "context":"application.context"
         },
         "instanceRef":"field",
         "createdBy":"user.guid",
         "date":"2015/08/01"
       },
       "form":{
         "data":{
           "activity_name":"Recruit Avengers",
           "language_code":"en",
           "start_date":"2001/01/01",
           "end_date":"",
           "activity_description":"Put together the team.",
           "objectives":[ 1, 2, 3]
         },
         "view":"opstools/ProcessApproval/views/testActivity.ejs",
         "viewData":{
           "objectives":[
             { "id":1, "objective_label":"Stop Hydra" },
             { "id":2, "objective_label":"Keep things compartmentalized" },
             { "id":3, "objective_label":"Tell Steve Rodgers what to do" },
             { "id":4, "objective_label":"Promote Colson" }
           ]
         }
       },
       "relatedInfo":{
         "view":"opstools/ProcessApproval/views/testRelated.ejs",
         "viewData":{
           "user":{
             "displayName":"Nick Fury",
             "avatar":"opstools/ProcessApproval/views/testAvatar.jpg",
             "teams":[1, 2, 3]
           },
           "teams":{
             "1": { "id":1, "team_name":"SHIELD Helicarrier", "team_description":"We fly around, but you can't see us." },
             "2": { "id":2, "team_name":"SHIELD Secret Base", "team_description":"Ssshhhhh ... it's secret." },
             "3": { "id":3, "team_name":"SHIELD HQ", "team_description":"Pretty cool place, really."}
           },
           "teamID":1
         }
       }
     },
     "comments":[1]
   },
   {
     "actionKey":"process.approval.tool.view",
     "userID":"user.2",
     "callback":"return.message.queue.1",
     "status":"pending",
     "objectData":{
       "menu":{
         "icon":".fa-calendar",
         "action": {
           "key":"[newActivity]",
           "context":"application.context"
         },
         "instanceRef":"field",
         "createdBy":"user.guid",
         "date":"2015/08/01"
       },
       "form":{
         "data":{
           "activity_name":"Secret Base",
           "language_code":"en",
           "start_date":"2001/01/01",
           "end_date":"",
           "activity_description":"Place to retreat ... just in case.",
           "objectives":[ 1, 2, 4]
         },
         "view":"opstools/ProcessApproval/views/testActivity.ejs",
         "viewData":{
           "objectives":[
             { "id":1, "objective_label":"Stop Hydra" },
             { "id":2, "objective_label":"Keep things compartmentalized" },
             { "id":3, "objective_label":"Tell Steve Rodgers what to do" },
             { "id":4, "objective_label":"Promote Colson" }
           ]
         }
       },
       "relatedInfo":{
         "view":"opstools/ProcessApproval/views/testRelated.ejs",
         "viewData":{
           "user":{
             "displayName":"Nick Fury",
             "avatar":"opstools/ProcessApproval/views/testAvatar.jpg",
             "teams":[1, 2, 3]
           },
           "teams":{
             "1": { "id":1, "team_name":"SHIELD Helicarrier", "team_description":"We fly around, but you can't see us." },
             "2": { "id":2, "team_name":"SHIELD Secret Base", "team_description":"Ssshhhhh ... it's secret." },
             "3": { "id":3, "team_name":"SHIELD HQ", "team_description":"Pretty cool place, really."}
           },
           "teamID":1
         }
       }
     },
     "comments":[2,3]
   },
   {
     "actionKey":"process.approval.tool.view",
     "userID":"user.2",
     "callback":"return.message.queue.1",
     "status":"pending",
     "objectData":{
       "menu":{
         "icon":".fa-calendar",
         "action": {
           "key":"[newActivity]",
           "context":"application.context"
         },
         "instanceRef":"field",
         "createdBy":"user.guid",
         "date":"2015/08/01"
       },
       "form":{
         "data":{
           "activity_name":"Secret Base",
           "language_code":"en",
           "start_date":"2001/01/01",
           "end_date":"",
           "activity_description":"Place to retreat ... just in case.",
           "objectives":[ 1, 2, 4]
         },
         "view":"opstools/ProcessApproval/views/notThere.ejs",
         "viewData":{
           "objectives":[
             { "id":1, "objective_label":"Stop Hydra" },
             { "id":2, "objective_label":"Keep things compartmentalized" },
             { "id":3, "objective_label":"Tell Steve Rodgers what to do" },
             { "id":4, "objective_label":"Promote Colson" }
           ]
         }
       },
       "relatedInfo":{
         "view":"opstools/ProcessApproval/views/testRelated.ejs",
         "viewData":{
           "user":{
             "displayName":"Nick Fury",
             "avatar":"opstools/ProcessApproval/views/testAvatar.jpg",
             "teams":[1, 2, 3]
           },
           "teams":{
             "1": { "id":1, "team_name":"SHIELD Helicarrier", "team_description":"We fly around, but you can't see us." },
             "2": { "id":2, "team_name":"SHIELD Secret Base", "team_description":"Ssshhhhh ... it's secret." },
             "3": { "id":3, "team_name":"SHIELD HQ", "team_description":"Pretty cool place, really."}
           },
           "teamID":1
         }
       }
     },
     "comments":[4]
   },
   {
     "actionKey":"process.approval.tool.view",
     "userID":"NotThisUser",
     "callback":"return.message.queue.1",
     "status":"pending",
     "objectData":{
       "menu":{
         "icon":".fa-calendar",
         "action": {
           "key":"[newActivity]",
           "context":"application.context"
         },
         "instanceRef":"field",
         "createdBy":"user.guid",
         "date":"2015/08/01"
       },
       "form":{
         "data":{
           "activity_name":"Secret Base",
           "language_code":"en",
           "start_date":"2001/01/01",
           "end_date":"",
           "activity_description":"Place to retreat ... just in case.",
           "objectives":[ 1, 2, 4]
         },
         "view":"opstools/ProcessApproval/views/notThere.ejs",
         "viewData":{
           "objectives":[
             { "id":1, "objective_label":"Stop Hydra" },
             { "id":2, "objective_label":"Keep things compartmentalized" },
             { "id":3, "objective_label":"Tell Steve Rodgers what to do" },
             { "id":4, "objective_label":"Promote Colson" }
           ]
         }
       },
       "relatedInfo":{
         "view":"opstools/ProcessApproval/views/testRelated.ejs",
         "viewData":{
           "user":{
             "displayName":"Nick Fury",
             "avatar":"opstools/ProcessApproval/views/testAvatar.jpg",
             "teams":[1, 2, 3]
           },
           "teams":{
             "1": { "id":1, "team_name":"SHIELD Helicarrier", "team_description":"We fly around, but you can't see us." },
             "2": { "id":2, "team_name":"SHIELD Secret Base", "team_description":"Ssshhhhh ... it's secret." },
             "3": { "id":3, "team_name":"SHIELD HQ", "team_description":"Pretty cool place, really."}
           },
           "teamID":1
         }
       }
     },
     "comments":[5]
   }
 ]

```

---
[< sprint 0](tutorial_sprint0_05_designFixtures.md)
