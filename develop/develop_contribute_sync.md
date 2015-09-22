[< Develop](Develop.md)
# Contributing: Keeping Your Fork in Sync with our project

As you begin working on **YOUR** fork of the project, changes will be made to the original Appdev version of the project.  

We recommend that you frequently (like every morning before you begin working on your changes) pull down any changes to the original project.  This way you reduce the headache of merging any conflicts in the future.


### Setup Our Project as an upstream repository (Only Once)
Now that you have your own copy of the project in your development directory, you need to link it back to the AppDev project.

1. Go to the Appdev GitHub project page and copy the github url link
2. add it as a git remote:
```sh
# assuming you are in your sails directory
$ cd node_modules/[project]
$ git remote add upstream [github url]
```
3. Now verify that it shows up in your list of remotes:
```sh
$ git remove -v
```


### Now synchronize our changes with yours (Daily)

```sh 
$ git fetch upstream
```

If you see a response like this:
```sh
remote: Counting objects: 5, done.
remote: Compressing objects: 100% (1/1), done.
remote: Total 5 (delta 4), reused 5 (delta 4), pack-reused 0
Unpacking objects: 100% (5/5), done.
From https://github.com/appdevdesigns/opstool-process-approval
   517fc06..445fd2b  develop    -> upstream/develop
```

Then you have changes that need to be merged with your local code:
```sh
$ git checkout develop
$ git merge upstream/develop
# if you have unfinished changes to #develop that you are not ready to push, then skip this next step:
$ git push origin develop
```


### Now your ready to get to work for the day.

  

[< Develop](Develop.md)     
Next: [Submit Pull requests >](develop_contribute_pull.md ) to our #develop branch