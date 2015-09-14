[< Models](develop_models.md)
# Models : Create the Server Side Model

### Prerequisits
Be sure to read up on [Sails' implementation of Models.](http://sailsjs.org/documentation/concepts/models-and-orm)

### Decide on a connection
Sails Models are configured with a ['connection' setting](http://sailsjs.org/documentation/reference/configuration/sails-config-connections).

Before you create your server, decide if your Model is able to reside alongside the default connection of the web site, or if your implementation requires it's own connection.


### How do you want your Database Managed?

Deciding how to create your server side Models depends on how you want your DB tables managed:

1. [let sails create and update the tables](develop_models_01_a_sailsManaged.md)
2. [connect to an existing table and have Sails access them](develop_models_01_b_existing.md)


[< Models](develop_models.md)     
Next: [let sails create and update the tables >](develop_models_01_a_sailsManaged.md)