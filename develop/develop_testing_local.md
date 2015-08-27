[< Develop](Develop.md)
# Testing Locally via NPM
The idea behind TDD is that you are going to be writing tests and running those tests often.  So each developer needs to be able to run the tests.

In your npm plugin we have modified the `package.json` definition to include a "test" command:
```json
"scripts": {
    "test": "make test",
    "postinstall": "node setup/setup.js",
    "postupdate": "node setup/setup.js"
},
```

This test command will run our `Makefile` in our project's root directory.  The `Makefile` sets up and runs mocha from the command line on our `[project]/tests/` directory.
```
REPORTER = dot

test:
	@NODE_ENV=test ./node_modules/.bin/mocha \
    --reporter $(REPORTER) \
    test/bootstrap.test.js \
    test/unit/*.js

.PHONY: test

```

So every time you add another testfile.js in your `[project]/test/` directory, that test file will automatically be run along with all the rest.

So to run your tests:
```sh
$ cd [project]
$ npm test
```




[< Testing and Continuous Integration](develop_testing.md)     
Next: [remotely using Travis CI >] (develop_testing_travisCI.md)