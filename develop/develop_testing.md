[< Develop](Develop.md)
# Testing and Continuous Integration
Our projects are setup to run various tests to help us ensure proper code integrity.

On our team we prefer to follow a more Test Driven Development (TDD) style of programming. You can read a nice summary of TDD [here](http://code.tutsplus.com/tutorials/the-newbies-guide-to-test-driven-development--net-13835).  

> Note: this example uses PHP, but the method and concepts are pretty solid.  
> We will describe how we implement things in our javascript frameworks



In summary our process would look like:

+ plan a new feature for development
+ write tests to define how that feature will act properly
+ see test fail
+ implement the code to accomplish the feature
+ see tests pass
+ clean up the code and make sure it still passes


### Our Implementation:
In order for us to accomplish this, we need to be able to write:

+ [Sails/Server side tests] ( develop_testing_server.md )
+ [client/UI side unit tests](develop_testing_client.md )
+ [UI Functional End-to-End tests] (develop_testing_endToEnd.md)


And we will need to be able to run the tests:

+ [locally via npm] ( develop_testing_local.md)
+ [remotely using Travis CI] (develop_testing_travisCI.md)

  
  

[< Develop](Develop.md)     
Next: [Sails/Server side tests >] ( develop_testing_server.md )