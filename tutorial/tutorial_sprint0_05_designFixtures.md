# Sprint 0 : Step 5: Design Fixtures

OK, now I'm ready to define some initial Design Fixtures to use as a reference set of data.

My goal here is to come up with a set of data that will act as a common reference point between our unit tests, our server side developers, and our client side developers.

If we decide to update the design specifications in the future, then this one file will need to be updated so it continues to represent the latest "correct" version of the specifications.

In our design, we are pulling our information from an `ApprovalRequest` model.  You need to create a sample set of data in : `test/fixtures/approvalrequests.json` 

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

  // ....  rest removed for brevity
]
```

In my initial set of test data, I included 4 entries:

+ 3 entries should match the test user's permissions
+ 1 entry with userID:'NotThisUser' should not
+ 1 matching entry returned will have an invalid template sent for the form data (client must handle this properly)

> NOTE: you can see the full set of fixture data [here](tutorial_sprint0_05_fullFixtureData.md).

Also, we are expecting to have some comments, so create another fixture file: `test/fixtures/approvalcomments.json` 
```json
[
  {
    "comment":"Do you think we should tell Captain America about the mission?",
    "response":"Keep it compartmentalized!",
    "request" : 1
  },
  {
    "comment":"Who do you want to get to run this thing?",
    "response":"Daryl!",
    "request" : 2
  },
  {
    "comment":"Should we tell Coulson?",
    "response":"Of course.  Just don't tell Cap. ",
    "request" : 2
  },
  {
    "comment":"Yeah, this entry shouldn't be shown.  it has an error.",
    "response":"",
    "request" : 3
  },
  {
    "comment":"This Request is for a user that isn't there.",
    "response":"",
    "request" : 4
  }
]
```
Finally, the test data needs to specify some default templates to use for the tests:

+ `opstools/ProcessApproval/views/testActivity.ejs`  for the object form
+ `opstools/ProcessApproval/views/testRelated.ejs`   for the related info form


---
[< sprint 0](tutorial_sprint0.md)
[Sprint 1 : Server Side >](tutorial_sprint1.md) 
