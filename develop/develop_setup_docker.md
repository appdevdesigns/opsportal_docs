Install Docker Toolkit

Windows http://docs.docker.com/engine/installation/windows/
Mac OS X http://docs.docker.com/engine/installation/mac/

Start the Quickstart Terminal

First, make sure you download and install [Git](https://git-scm.com/downloads) and [Mysql](https://www.mamp.info/en/downloads/).
You will need to install MySQL in order to install the Ops Portal. You can either install it directly to your preferred OS or use Docker to install it.

To install it with Docker, run the following command from the docker terminal:
# docker run --name mysqlsails -e MYSQL_ROOT_PASSWORD=root -d -p 3306:3306 mysql:latest
This will download it (the first time) and start it running. The environment variable setting the password is required when running it. For now, when using Docker, it seems you have to use port 3306. Remember to specify 3306 later on setting the port for Sails.

No matter how you install MySQL, you will need to create a database to use with Ops Portal. The default name is Develop.

To create a database using Docker, first get access to the container by:
# docker exec -it mysqlsails /bin/bash

This creates a shell into the Mysql container. Now at the Root shell command line, create the database by: (it will prompt for password which is “root” from above)
# mysqladmin -p create develop
Type exit to get out of the shell.

Now you can get and run the Docker container for the Ops Portal by:
# docker run -d --name opsportal -p 1337:1337 ricadn/opsportal:latest

Now you can see both processes by:
# docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
37185bfe5273        mysql:latest        "/entrypoint.sh mysql"   About an hour ago   Up About an hour    0.0.0.0:3306->3306/tcp   mysqlsails
746491e7b6ee        opsportal:latest    "supervisord -n"         2 hours ago         Up 2 hours          0.0.0.0:1337->1337/tcp   opsportal

Find your IP address (when your are running the ops portal using Docker, you must use its IP address in the browser rather than localhost)
# docker-machine ip default    Take note of it to be used later.

Now we simply need to run the appdev install tool:

```sh
$ docker exec -it opsportal /bin/bash
$ appdev install sails --develop

    # you will be asked numerous configuration questions.
    # Use your IP address instead of localhost
    # In many cases you can simply [enter] to accept the default
    # options:
    #     mysql : setup according to typical MAMP settings
    #     SSL   : no
    #     auth  : local
```

Test it out

Now test this out:

```sh
$ cd sails
$ sails lift
```


You should see a response like:

```sh
info: Starting app...

info:
info:
info:    Sails              <|
info:    v0.10.5            |\
info:                      /|.\
info:                      / || \
info:                    ,'  |'  \
info:                .-'.-==|/_--'
info:                `--'-------'
info:    __---___--___---___--___---___--___
info:  ____---___--___---___--___---___--___-__
info:
info: Server lifted in `/path/to/your/dir/sails`
info: To see your app, visit http://localhost:1337
info: To shut down Sails, press <CTRL> + C at any time.

debug: --------------------------------------------------------
debug: :: Fri Feb 13 2015 17:24:55 GMT+0700 (ICT)

debug: Environment : development
debug: Port        : 1337
debug: --------------------------------------------------------

```


Open a web browser and enter the path: yourIP:1337/page/opsportal

You should see a spiffy web page with a bar across the bottom for the Opsportal. Clicking on the bar will cause the OpsPortal to take over the whole screen. Clicking on the menu icon will allow you to switch between the installed OpsTools.

In order to leave Sails running, start another Quickstart Terminal.

Then type the command:
# docker exec -it opsportal /bin/bash

By default, you will be in the /data/app/ directory inside the VM where you installed your Sails app.

If you successfully got here, then you are ready to create your first OpsTool Plugin.

[< Develop](Develop.md)     
Next: [Create a Plugin >](develop_plugin_create.md)