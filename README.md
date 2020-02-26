
# Setting up docker on Windows 10

## Installing docker

Go to:

[https://hub.docker.com/editions/community/docker-ce-desktop-windows/](https://hub.docker.com/editions/community/docker-ce-desktop-windows/)

Click on "Get Docker Desktop for Windows (stable)".
It will download a file of \~1GB.

While it's downloading, go to [https://hub.docker.com](https://hub.docker.com) and create an account. This will make the installation easier. If you don't want to create an account, you don't need to, but it will add an extra step to the installation.

Start the installation and make sure to NOT check the box saying:
"Use windows containers instead of linux containers"
Because we want to use linux containers.

When installation is done, it will ask to restart your computer.

## Configuring docker

Start docker desktop. You will have a notification saying:

"Docker Desktop is starting..."

Wait for some time. It may take a minute, but it is only for the first time it starts.

When it's ready it will open a window asking you to log in. If you created an account at dockerhub, you can authenticate. Otherwise, you can just close this window.

Right click the docker icon (close to the clock on the bottom right) and click on "Settings"

Go to “Resources” -> “File Sharing” and enable the disks you want to share with docker (C), then press "Apply & Restart"

## Extra step (only if you didn't create a dockerhub account)

If you didn't create an account in dockerhub, there is an extra step.

Open `C:\Users\<your user>\.docker\config.json` in a text editor

Delete the line

`"credsStore": "desktop"`

So the full file becomes only:

```
{
    "stackOrchestrator": "swarm"
}
```

Save the file and now you are ready!

# Running the nginx+php+mysql stack

Download a zip from (or clone):

[https://github.com/gbuzogany/docker-nginx-php-mysql](https://github.com/gbuzogany/docker-nginx-php-mysql)

Then extract it. Update the `.env` file inside the project folder if you want. In most cases you may want only to change the MySQL credentials configuration, and the name for the default database (`MYSQL_DATABASE`).

Open power shell as an administrator, go to the folder you just extracted and run::

`docker-compose up -d`

It will download everything it needs and start it when it's ready. The first time it starts may take some time.
If the Windows firewall asks for permission for docker, make sure you authorise.

[http://localhost:8000/](http://localhost:8000/) -> Your PHP website  
[http://localhost:8080/](http://localhost:8000/) -> PHPMyAdmin

It should also work from the local network.

To change the PHP files, the equivalent of the `www` folder is the `web/public/` folder.

When connecting to mysql from PHP, remember that mysql is running in a separate container, so it’s not “localhost”. The mysql hostname for the connection is defined on the `.env` file in the project folder (`MYSQL_HOST`).

# Using docker

Whenever you want to check which containers are running, just run from powershell, as an admin:
`docker ps`

It will list which containers are running. For example:

```
PS C:\Users\admin\Desktop\docker-nginx-php-mysql-master> docker ps
CONTAINER ID        IMAGE                      COMMAND                  CREATED             STATUS              PORTS                                         NAMES
8262bf671d8f        nginx:alpine               "/bin/sh -c 'envsubs…"   33 hours ago        Up 20 minutes       0.0.0.0:8000->80/tcp, 0.0.0.0:3000->443/tcp   docker-nginx-php-mysql-master_web_1
5ce5242d427a        phpmyadmin/phpmyadmin      "/docker-entrypoint.…"   33 hours ago        Up 20 minutes       0.0.0.0:8080->80/tcp                          phpmyadmin
f1ef08d3bb13        nanoninja/php-fpm:latest   "docker-php-entrypoi…"   33 hours ago        Up 20 minutes       9000/tcp                                      docker-nginx-php-mysql-master_php_1
c696b69eeca2        mysql:5.7.22               "docker-entrypoint.s…"   33 hours ago        Up 20 minutes       0.0.0.0:8989->3306/tcp                        mysql
```

You can handle the containers either by the `CONTAINER ID` or `NAME`.

To stop a container:  
`docker stop 8262bf671d8f`  
The command has the same effect as:  
`docker stop docker-nginx-php-mysql-master_web_1`

To restart a container:  
`docker restart 8262bf671d8f`

To list all containers (even stopped ones):  
`docker ps -a`

To remove a container (after stopping it):  
`docker rm 8262bf671d8f`

## Starting and stopping
To start the containers again, run the following command from the project folder:  
`docker-compose up -d`

To stop the containers:  
`docker-compose stop`

To destroy the containers:  
`docker-compose down`

