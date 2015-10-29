[< Tutorial](tutorial.md)
# Tutorial - Sprint 1


### Overview
Last sprint we just did the initial setup of the project.  Now in this sprint we are going to begin the process of building out the design. 


### Prerequsite
I'm expecting that you have already walked through our [Sprint 0](tutorial_sprint0.md).

If not, you can download this project as it looked after sprint 0:
```sh
// from your Sails directory
$ cd node_modules
$ git clone https://github.com/appdevdesigns/opstool-process-approval.git
$ cd opstool-process-approval
$ git checkout Sprint_0
```

### Status
Last sprint we sent our UI design to our UI developer.  However as of right now, we still do not have his designs completed and returned to us.  So on this sprint we will focus mostly on getting the server side models setup and preparing the interface for an external tool to submit an authorization request.

### Let's Go

+ [step 1](tutorial_sprint1_01_models.md) : Create the Models on the server
+ [step 2](tutorial_sprint1_02_fixtures.md) : Correct our Fixture data
+ [step 3](tutorial_sprint1_03_fixtureResponse.md) : Return our expected Fixture data on PARequest.find()
+ [step 4](tutorial_sprint1_04_serverBranch.md) : Create our Server Side Feature branch
+ [step 5](tutorial_sprint1_05_lockdownAPI.md) : Lockdown unused interfaces
+ [step 6](tutorial_sprint1_06_restrictAccess.md) : limit access to the find() operation to users with the proper permission
+ [step 7]() : condition the find() operation to limit according to the user's scope
+ [step 8]() : create the server side Message Queue

///// left off here::
////  + make sure appdev install ...  auto set's up admin:admin user account by default (in local Auth)
////

---
[< Tutorial](tutorial.md)
[Sprint 1 : Create the Models on the server >](tutorial_sprint1_01_models.md) 