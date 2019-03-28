## Linux Server Configuration Project
### Full Stack Web Developer Nanodegree - Udacity

In this project we will cover all the steps necessary to install a Linux server and host a web application with a postgres database.

The web application we'll be hosting is the `catalog-app` created during the Project # 2 of this course.
The full application code for the app and all the necessary steps to configure it for production can be found at this [git repository](https://github.com/pierva/catalog-app).

The preview of the running application can be found at this address:
www.piervaleriovignola.com

To learn all the application features and how it works please refer to the app [repository readme](https://github.com/pierva/catalog-app).

#### Hosting Server Details

public (static) ip: 34.225.146.144
<br>
port: 2200
<br>
uri: http://www.piervaleriovignola.com

## 1. Getting Started With Lightail
Lightail is the easiest way to get started with AWS. It allows you to launch a virtual private server in just few clicks. You don't have to worry about all the infrastructure setup.

Login into your [Lightail account](https://lightsail.aws.amazon.com/ls/webapp/home/instances) and in the home page click on "create instance" button.

![alt lightsail home](/images/lightsail_home.png)

In the next page select the Instance Location (recommended to select the closest location from you), then in *Select Platform* choose `Linux/Unix` and in the *Select a blueprint* click on `OS Only` and select `Ubuntu`.

![alt new instance](images/New_Instance.png)

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

## 2. Connect using your own SSH client application

### 2.1 Create a static IP
It is recommended to create a static IP address for your machine before setting up the key-pairs authentication process.

Click on the newly created instance name to enter the management page. Alternatively you can enter the management page by clicking on the vertival ellipsis next to the machine name and select `Manage`.

![alt instance menu](images/manage.png)

In the new page, select the *Networking* tabk and click on `Create static IP`.

![static_ip](images/static_ip.png)

After clicking the button a new page will open. In this page the default settings in the top part are generally fine, you can change the Static IP location if you want.
Give a name to your static IP if you prefer and click on `Create`.

![alt new static ip](images/new_static_ip.png)

You'll get a new *Public static IP address*. This is what we'll use to connect to the machine.

![alt static ip page](images/static_ip_page.png)


### 2.2 Create the private key-pair

We need to create a key pair that will be used to authenticate to the Virtual Machine.
In your client terminal type the following command:

```sh
$ ssh-keygen
```
You'll be prompted to enter the file in which to save the key:
```sh
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/user/.ssh/id_rsa): /Users/user/.ssh/YOUR-INSTANCE-KEY
```
Then you'll be asked to enter a passphrase for the new key. Recommended to not leave it empty.

After typing the password, two files will be generated in the location you specified.
You can check it by typing:
```sh
ls ~/.ssh
```

You'll see something like this:
```sh
$ ls
YOUR-INSTANCE-KEY
YOUR-INSTANCE-KEY.pub
```
Let's copy the public key in the clipboard so we can paste it in the virtual machine.

```sh
sudo cat ~/.ssh/YOUR-INSTANCE-KEY.pub
```
You should be prompted for a password, type your local machine (mac/windows) password.

Copy the output string that begins with "ssh-rsa ...."

Go to the aws [lightsail console](https://lightsail.aws.amazon.com/ls/webapp/home/instances) and connect to the instance from using the terminal icon or by clicking on `Connect using SSH` inside the instance management page.

![alt connet ssh](images/connect_ssh.png)

In the terminal let's navigate in the `.ssh` folder:

```sh
$ cd .ssh
```

The fastest way to upload the file is to remove the existing `authorized_keys` file, create a new one and paste the copyed public key.

```sh
$ sudo rm authorized_kyes

$ sudo nano authorized_keys
```
To paste in this terminal:
1. press the clipboard button in the toolbard (bottom right)
2. paste the content in the text area
3. close the clipboard by clicking the same button
4. focus on the terminal (click on the terminal) and right click with the mouse

![alt clipboard](images/clipboard.png)
Now you can save and close the file `ctrl+x`, `Y`, and `enter`.

One last thing we need to do to do is set up some specific file permissions on the authorized key file and SSH directory.
This is a security measure that SSH enforces to ensure other users cannot gain access to your account.

```sh
$ sudo chmod 700 ~/.ssh
$ sudo chmod 644 ~/.ssh/authorized_keys
```

### 2.3 Connect from your terminal
Now that we have a strong authentication process in place we can connect to the machine directly from the client terminal (PuTTY).
In your terminal type the below command:

```sh
$ ssh ubuntu@xx.xxx.xxx.xxx -i ~/.ssh/YOUR-INSTANCE-KEY
```

Insert your IP address and make sure that the path and file name to your private key is correct.

## 3. Change ssh port
Now that we are logged in we can go ahead and setup our server as we like.

Let's start by changing the SSH port from 22 to 2200.
To make this change we need to change the port number in the `sshd_config` file.

```sh
sudo nano /etc/ssh/sshd_config
```

Locate the line that says
```
# What ports, IPs and protocols we listen for
Port 22
```
and change 22 to 2200.
```
# What ports, IPs and protocols we listen for
Port 2200
```
Save and exit. Restart the sshd service.
```sh
sudo service sshd restart
```
Before we can use the port we need to enable it in the instance firewall in the [aws lightsail console](https://lightsail.aws.amazon.com/ls/webapp/us-east-1/instances/YOUR-INSTANCE-NAME/networking).
Inside your `instance` page select the *Networking* tab and under the *Firewall* section click on `+ Add another`.

![alt firewall settings](images/firewall_settings.png)
Let's test that the port is actually working.

Insert the new port 2200 in the Port range. Application should be Custom and Protocl TCP. Save the new configuration.
![alt new port](images/new_port.png)


Now we can test if everything is working as it should. Exit the machine if not already done. From your client terminal:
```sh
exit
```
Now let's try the SSH with the new port (Insert your IP and the path to the secret key).

```sh
$ ssh ubuntu@xx.xxx.xxx.xxx -p 2200 -i ~/.ssh/YOUR-INSTANCE-KEY
```
If you're prompted to enter the password and then you enter the machine you're all set.

```sh
$ ssh ubuntu@xx.xxx.xxx.xxx -p 2200 -i ~/.ssh/YOUR-INSTANCE-KEY

Enter passphrase for key '/Users/piervalerio/.ssh/YOUR-INSTANCE-KEY':
Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.4.0-1052-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

New release '18.04.2 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


Last login: Tue Mar 26 23:10:12 2019 from 170.250.216.229
ubuntu@ip-xxx-xx-xx-xx:~$
```

## 4. Configure the timezone to UTC
To set the server clock to UTC (default) we can type the following command:
```sh
sudo dpkg-reconfigure tzdata
```
This will open the setting page where you can choose the timezone for the machine.

Select `None of the above` in the first page.

![alt clock settings](images/clock_1.png)

In the subsequent page select UTC and click ok.
![alt clock to UTC](images/clock_2.png)

____

## 5. Configuring the server firewall rules
Now that we have configured our server as we like is time to configure and enable the virtual machine firewall.

Check the firewall status with:
```sh
$ sudo ufw status
```
By default the firewall is inactive.

**Before enabling the firewall make sure that is configured correctly, otherwise you may risk to be locked out of your lightsail instance**

The port we want to enable are:
* 80 (HTTP)
* 2200 (SSH)
* 123 (NTP)

Let's start by disabling all the incoming ports and enabling all the default outgoing.

```sh
$ sudo ufw default deny incoming
$ sudo ufw default allow outgoing
```
Now we can enable our ports:
```sh
$ sudo ufw allow www
$ sudo ufw allow 2200/tcp
$ sudo ufw allow 123/udp
```
If you're completely sure that the rules are set up properly, you can now enable the firewall.
```sh
$ sudo ufw enable
```

If you now run the command `sudo ufw status`, you should get the following output:
```
Status: active

To                         Action      From
--                         ------      ----
80/tcp                     ALLOW       Anywhere                  
2200/tcp                   ALLOW       Anywhere                  
123/udp                    ALLOW       Anywhere                  
80/tcp (v6)                ALLOW       Anywhere (v6)             
2200/tcp (v6)              ALLOW       Anywhere (v6)             
123/udp (v6)               ALLOW       Anywhere (v6)
```

## 6. Create a new User
In this section we'll create a new user called `grader`.
To create our new user it is necessary to have `sudo` rights. If we are logged in as ubuntu we're fine. When we add the new user we'll be prompted with few questions including the password for the new user.

```sh
$ sudo adduser grader

Adding user `grader' ...
Adding new group `grader' (1001) ...
Adding new user `grader' (1001) with group `grader' ...
Creating home directory `/home/grader' ...
Copying files from `/etc/skel' ...
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
Changing the user information for grader
Enter the new value, or press ENTER for the default
	Full Name []: grader
	Room Number []:  
	Work Phone []:
	Home Phone []:
	Other []:
Is the information correct? [Y/n] y
```
### 6.1 Give the new user sudo privileges
Ubuntu creates a folder with all the users with pseudo privileges. You can check who has pseudo privileges with the following command.

```sh
$ sudo ls /etc/sudoers.d
```

In this folder you'll find a default user called `90-cloud-init-users`.

1. Let's make a copy of this file.

```sh
$ sudo cp /etc/sudoers.d/90-cloud-init-users /etc/sudoers.d/grader
```

2. Open the file
```sh
$ sudo nano /etc/sudoers.d/grader
```
3. Change ubuntu with the new user: grader
```
# User rules for ubuntu
grader ALL=(ALL) NOPASSWD:ALL
```

4. Save and close the file

5. If you want to force the new user to change the password after the login, type the following command:

```sh
$ sudo passwd -e grader
```
The `-e` flag will basically expire the password.

### 6.2 Create a strong key-pair authentication
Follow the steps described in section 2.2

### 6.2 Access the server as the new user

Access the server as `grader` (private key provided in the note for the instructor):
```sh
$ ssh grader@34.225.146.144 -p 2200 -i ~/.ssh/<private-key-file>
```
The ssh authorized keys file is located in the following directory `/.ssh`:
```sh
$ cd ~/.ssh/authorized_keys
```

## 7. Installing PostgreSQL
Installing and setup the database on the linux machine can be somehow tricky. We'll break it down all the necessary steps in order to make the process as simple as possible.

Let's start by updating the machine.
```sh
$ sudo apt-get update
$ sudo apt-get upgrade
```

### 7.1 Installtion
```sh
$ sudo apt-get install postgresql postgresql-contrib
```
This will install the latest version of the postgresql server locally. Additionally togheter with the server it will be installed all the commonly used add-ons for it.

To start we need to create the role postgres and setup the password.

```sh
$ sudo -u postgres psql postgres
```
Set up up the password:
```
\password postgres
```
Type `Ctrl+D` or `\q` to exit the postgreSQL prompt.

We now have PostgreSQL installed and the PostgreSQL service is running in the background. However, we need to create a user and a database instance for our needs to really start using it.

`postgres` is a super user. We want to create user that has limited access only to the defined database.

Use the sudo command to switch to the "postgres" account.
```sh
$ sudo -i -u postgres
```

Within the "postgres" account, create a user from the command line with the createuser command. PostgreSQL will prompt you with several questions. Answer "n" to superuser and "y" to the other questions.
```sh
$  createuser catalog -P --interactive
```

The output will be something like this:
```sh
postgres@ip-172-26-15-52:~$ createuser catalog -P --interactive
Enter password for new role:
Enter it again:
Shall the new role be a superuser? (y/n) n
Shall the new role be allowed to create databases? (y/n) y
Shall the new role be allowed to create more new roles? (y/n) y
postgres@ip-172-26-15-52:~$
```
Exit out of the postgres account by pressing the "Ctrl+D" into the terminal shell.

Create a new database:
```sh
sudo -u postgres createdb -O catalog catalogdb
```
The above command will create a database `catalogdb` with `catalog` as owner.

To check get the list of roles in postgreSQL type:
```sh
sudo -u postgres psql catalogdb
```
And then inside the postgres prompt:
```
SELECT rolname FROM pg_roles;
```

To get the list of all the databases, in the same postgreSQL prompt type:
```
\l
```

### 7.2 Disable remote connections
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
### 7.3 Test the local connection
To communicate with our database within python we need to install a module called `psycopg2`.
To install python3 modules with `pip3` we need to install `pip3` first and upgrade it.
```sh
$ sudo apt-get install python3-pip
$ sudo pip3 install --upgrade pip
```

Install psycopg2:
```sh
$ sudo pip3 install psycopg2
$ sudo pip3 install psycopg2-binary
```

Now we can open python3, import the module and test the connection with the db.

You can use the below code to test the connection. Replace the connection string values with your values.

```python
import psycopg2

try:
    connect_str = "dbname='catalogdb' user='catalog' host='localhost' " + \
                  "password='catalog'"
    # use our connection values to establish a connection
    conn = psycopg2.connect(connect_str)
    # create a psycopg2 cursor that can execute queries
    cursor = conn.cursor()
    # create a new table with a single column called "name"
    cursor.execute("""CREATE TABLE IF NOT EXISTS tutorials (name char(40));""")
    cursor.execute("INSERT INTO tutorials (name) VALUES ('Linux Server Tutorial');")
    conn.commit() # <--- makes sure the change is shown in the database
    cursor.execute("""SELECT * from tutorials""")
    rows = cursor.fetchall()
    conn.close()
    cursor.close()
    print(rows)
except Exception as e:
    print("Uh oh, can't connect. Invalid dbname, user or password?")
    print(e)
```
We can open the database and confirm that the table was created.

```sh
$ sudo -u postgres psql catalogdb
```
```
catalogdb=# \dt
```
Now we can remove the table (inside the psql prompt) with `DROP TABLE`:
```
catalogdb=# DROP TABLE tutorials;
```


## 8. Install Apache2

Get and install apache2
```sh
$ sudo apt-get install apache2
```
If apache is set correctly, you'll see the welcome page when you navigate to the public_IP from the browser.

We also need WSGI in our server.

mod_wsgi is an Apache HTTP Server module by Graham Dumpleton that provides a WSGI compliant interface for hosting Python based web applications under Apache.

Install mod_wsgi for python3:
```sh
$ sudo apt-get install libapache2-mod-wsgi-py3
```
After the installagtion wsgi is already enable. However, just to make sure that is enable we can try enableing it with:
```sh
$ sudo a2enmod wsgi
```
```sh
Restart apache server:
$ sudo service apache2 restart
```

## 9. Set up the Flask Application

We'll now use the [catalog-app](https://github.com/pierva/catalog-app) application previously created during the course and make sure it is served publicly on the browser by our server.

Please follow the steps indicated in the [catalog-app](https://github.com/pierva/catalog-app) README to proper setup the application for production.


In apache2, the applications are by default served from the `/var/www/html` folder. If you have subdomains you can set up a different directory where WSGI will look for the application.

This is done by creating a new virtual host conf file in `/etc/apache2/sites-available`.

The configuration guide on how to setup the virtual host can be found [here](https://modwsgi.readthedocs.io/en/develop/user-guides/quick-configuration-guide.html)

### 9.3 Clone the application
Navigate in the `html` directory and clone the application.

```sh
$ cd /var/www/html
$ sudo git clone https://github.com/pierva/catalog-app
```

### 9.4 Create and setup the WSGI file
Inside the html folder we need to create the wsgi file for our app.

```sh
$ sudo nano /var/www/html/catalog_app.wsgi
```

Paste the following code.
```python
import sys
import logging

logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, '/var/www/html/catalog-app')

from catalog import app as application

```

Error log can be found at:
```sh
sudo cat /var/log/apache2/error.log
```
___


Library: <br>
[Secure postgres on ubuntu](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps)
<br>
[SQLAlchemy engine configuration](https://docs.sqlalchemy.org/en/latest/core/engines.html#postgresql)
<br>
[Downlaod and setup PuTTY](https://lightsail.aws.amazon.com/ls/docs/en/articles/lightsail-how-to-set-up-putty-to-connect-using-ssh)
[Copy paste in lightsail terminal](https://forums.aws.amazon.com/thread.jspa?messageID=814852&tstart=0)
[PostgreSQL roles](http://www.postgresqltutorial.com/postgresql-roles/)
[Drop table](http://www.postgresqltutorial.com/postgresql-drop-table/)
[Listing db](https://chartio.com/resources/tutorials/how-to-list-databases-and-tables-in-postgresql-using-psql/)
