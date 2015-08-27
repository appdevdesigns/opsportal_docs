[< Testing and Continuous Integration](develop_testing.md) 
# Testing Remotely using Travis CI
The last piece of our testing puzzle is making sure that changes made from all of our developers and contributers still work together and all our tests pass.

We do this by integrating our github projects with [Travis CI] (https://travis-ci.org).  

Now each time we push changes to the project or a Pull request is made to the project, we can verify the code changes don't break any of our tests.

In our project's root directory we have a `[project]/.travis.yml`  configuration file to tell TravisCI how to run our tests.

Our main challenge in setting up a TravisCI build for one of our tools, is that an OpsPortal plugin needs to be running within a Sails project that already has our OpsPortal setup.  

Here is how our default `[project]/.travis.yml` attempts to accomplish that:
```
language: node_js
node_js:
- "stable"

before_script:
- npm install -g  balderdashy/sails appdevdesigns/appdev-cli#develop
- cd /tmp
- chmod +x /home/travis/build/appdevdesigns/[projectName]/test/setup/install.sh
- /home/travis/build/appdevdesigns/[projectName]/test/setup/install.sh
- cd ad-test/node_modules
- mv /home/travis/build/appdevdesigns/[projectName] .
- cd [projectName]
- npm install mocha chai
- npm install

script:
- npm test
```



###### first we specify we need to use the latest stable version of NodeJS:
```
language: node_js
node_js:
- "stable"
```


###### before we run our script, we need to install  SailsJS and our Appdev tools:

```
before_script:
- npm install -g  balderdashy/sails appdevdesigns/appdev-cli#develop
```


###### Then we need to setup a default Sails + Appdev install:
```
- cd /tmp
- chmod +x /home/travis/build/appdevdesigns/opstool-process-approval/test/setup/install.sh
- /home/travis/build/appdevdesigns/opstool-process-approval/test/setup/install.sh
```
> NOTE: the `install.sh` script runs any additional steps for the setup.
> By default it simply runs: `appdev install ad-test --develop --travisCI`


###### Now we move our project under the newly installed `ad-test` directory:
```
- cd ad-test/node_modules
- mv /home/travis/build/appdevdesigns/[projectName] .
```


###### And now enter our project directory and make sure all the `npm` dependencies are installed:
```
- cd [projectName]
- npm install mocha chai
- npm install
```


###### Now we are ready for TravisCI to run our tests:
```
script:
- npm test
```



[< Testing and Continuous Integration](develop_testing.md)     
