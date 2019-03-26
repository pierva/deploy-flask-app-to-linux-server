## Linux Server Configuration Project
### Full Stack Web Developer Nanodegree - Udacity

In this project we will cover all the steps necessary to install a Linux server and host a web application with a postgres database.

The web application we'll be hosting is the `catalog-app` created during the Project # 2 of this course.
The full application code for the app and all the necessary steps to configure it for production can be found at this [git repository](https://github.com/pierva/catalog-app).

The preview of the running application can be found at this address:
www.piervaleriovignola.com

To learn all the application features and how it works please refer to the app [repository readme](https://github.com/pierva/catalog-app).

#### Hosting Server Details

public ip: 3.209.74.0
<br>
port: 2200
<br>
uri: http://www.piervaleriovignola.com

## 1. Getting Started With Lightail
Lightail is the easiest way to get started with AWS. It allows you to launch a virtual private server in just few clicks. You don't have to worry about all the infrastructure setup.

Login into your [Lightail account](https://lightsail.aws.amazon.com/ls/webapp/home/instances) and in the home page click on "create instance" button.

![alt lightsail home](/images/lightsail_home.png)

In the next page select the Instance Location (recommended to select the closest location from you), then in *Select Platform* choose `Linux/Unix` and in the *Select a blueprint* click on `OS Only` and select `Ubuntu`.

![alt new instance](/images/new_instance.png)

Keep all the default settings for now
*Choose your instance plan*, give a name to your instance and finally click on `Create Instance`.

![alt instance plan](/images/Instance_plan.png)

After clicking the button, the new instance will be created. The process will take few seconds to complete.
In the home page now you'll see the new instance.

![alt new instance created](/images/created_instance.png)

To connect to the instance click on the terminal icon ![alt icon](/images/terminal_icon.png)

A new terminal window (within a browser) will open and you're connected to your machine.

![alt new terminal](/images/terminal_window.png)

It is not very easy to work with this terminal in the window, because some operations such us copy-paste won't work. We'll set up the connection to the new machine from your own SSH client (terminal/PuTTY).


## Grader User
The `grader` user was created for the instructors' review.
Access the server as `grader` (private key provided in the note for the instructor):
```sh
$ ssh grader@3.209.74.0 -p 2200 -i ~/.ssh/<private-key-file>
```
The ssh authorized keys file is located in the following directory `/.ssh`:
```sh
$ cd ~/.ssh/authorized_keys
```

## Installing PostgreSQL
Installing and setup the database on the linux machine can be somehow tricky. We'll break it down all the necessary steps in order to make the process as simple as possible.

### Disable remote connections
Remote connections are by default disabled when installing PostgreSQL from the Ubuntu repositories. We can double check it by accessing this configuration file:
```sh
sudo nano /etc/postgresql/9.1/main/pg_hba.conf
```
Make sure that all the connections point to localhost (127.0.0.1/32 and ::1/128). The configuration file should have these allowed connections only:

```sh
# Database administrative login by Unix domain socket
local   all             postgres                                peer

# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     peer
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
# IPv6 local connections:
host    all             all             ::1/128                 md5
```


Library: <br>
[Secure postgres on ubuntu](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps)
<br>
[SQLAlchemy engine configuration](https://docs.sqlalchemy.org/en/latest/core/engines.html#postgresql)
