 EdX Installation on Ubuntu 12.04 
=======================================
* **sudo apt-­get update ­-y**

* **sudo apt-­get upgrade ­-y**

* **reboot**

* **sudo apt-get install -y build-essential software-properties-common python-software-properties curl git-core libxml2-dev libxslt1-dev python-pip python-apt python-dev**

* **wget https://bitbucket.org/pypa/setuptools/raw/0.8/ez_setup.py ­O ­ | sudo python**

* **sudo pip install ­­--upgrade pip**

* **sudo pip install ­­--upgrade virtualenv**

* **cd /var/tmp**

* **git clone -­b release https://github.com/edx/configuration**

* **cd /var/tmp/configuration**
* **sudo pip install ­-r requirements.txt**

* **cd /var/tmp/configuration/playbooks**

* **sudo ansible-­playbook ­-c local ./edx_sandbox.yml ­-i "localhost,"**

    **or** instead of localhost you can use serverip as below

* **sudo ansible­-playbook -­c local ./edx_sandbox.yml -­i “x.x.x.x,"**

##  Issues in edX Ubuntu 12.04 Installation

###1) Error in creating database for edxapp:
  
```ruby 
TASK : [edxlocal | create a database for edxapp] ************************************

```


**Sol**

create file .my.cnf  with database username and password as similar to following:  
sudo gedit /root/.my.cnf 

 ```

[client]

user=root

password=xxxxx

```

###2)Error in installing mongodb:
```ruby
TASK: [mongo | install mongo server and recommends] ***************************

msg: 'apt­-get install 'mongodb-­10gen=2.4.7' '
failed: E: There are problems and -­y was used

without –force-yes
```
**Sol**

a)sudo apt-­get remove mongodb­-clients

b)sudo apt­-get install mongodb-­10gen=2.4.7

###3)Error in installing npm:
```ruby
msg: 'apt­-get install 'npm' 'npm' 'gettext' ' failed:

The following packages have unmet dependencies:

npm : Depends: nodejs (>= 0.6.19~dfsg1­3) but it is not going to be installed

Depends: node-js­dev

Depends: node­-node-­uuid but it is not going to be installed

Depends: node-­request but it is not going to be installed

Depends: node-­mkdirp but it is not going to be installed

Depends: node-­minimatch but it is not going to be installed

Depends: node-­semver but it is not going to be installed

Depends: node­-ini but it is not going to be installed

Depends: node­-graceful­fs but it is not going to be installed

Depends: node­-abbrev but it is not going to be installed

Depends: node­-nopt but it is not going to be installed

Depends: node­-fstream but it is not going to be installed

Depends: node-­rimraf but it is not going to be installed

Depends: node-­tar but it is not going to be installed

Depends: node­-which but it is not going to be installed

E: Unable to correct problems, you have held broken packages.
```
**Sol**

a)sudo apt­-get remove nodejs nodejs-­dev

b)Download nodejs_0.6.12~dfsg1­1ubuntu1_i386.deb (663.7 KiB) from

https://launchpad.net/ubuntu/precise/i386/nodejs/0.6.12~dfsg1­1ubuntu1

click the package and install through Ubuntu Software Center by clicking install button

c)Download nodejs­-dev from http://packages.ubuntu.com/precise/i386/nodejs­dev/download

(ubuntu.cs.utah.edu/ubuntu or any other )

click the package and install through Ubuntu Software Center by clicking install button

d)sudo apt­-get install npm

###4)Error in installing nltk

**Sol**

sudo /edx/app/ora/venvs/ora/bin/python -­m nltk.downloader ­-d /edx/var/ora/nltk_data all

###5)Error in installing pika
```ruby
msg:Command /usr/bin/git clone -­q git://github.com/pika/pika.git /edx/app/xqueue/venvs/xqueue/src/pika/

failed with error code 128 in None

Storing complete log in /root/.pip/pip.log

:stderr: fatal: unable to connect to github.com:
```
**Sol**

a)sudo gedit /edx/app/xqueue/xqueue/requirements.txt

replace

­-e git://github.com/pika/pika.git@a731347fc0#egg=pika

with

pika

b)sudo gedit /var/tmp/configuration/playbooks/roles/xqueue/tasks/deploy.yml

and comments the following
```
 Do A Checkout

 name: xqueue | git checkout xqueue repo into {{app_base_dir}}

 git: dest={{xqueue_code_dir}} repo={{xqueue_source_repo}} version={{xqueue_version}}
```

###6)Error in installing discern
```ruby
IOError: Could not build the egg.

­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­

Cleaning up...

Command python setup.py egg_info failed with error code 1 in /edx/app/discern/venvs/discern/build/MySQL-­python

Storing debug log for failure in /edx/app/discern/.pip/pip.log
```
Sol

a)cd /edx/app/discern

b)git clone https://github.com/defance/discern.git

Or

a)sudo gedit /var/tmp/configuration/playbooks/roles/discern/defaults/main.yml

b)changed the value of variable 'discern_source_repo' to 'https://github.com/defance/discern.git'

###  7) Internal Server Error in CMS running

**Sol**

a) cd /edx/app/edxapp/edx­platform

* list all manage.py commands ­

sudo ­-u www-­data /edx/bin/python.edxapp ./manage.py lms ­­--settings aws help

* create a new user ­

sudo ­-u www­-data /edx/bin/python.edxapp ./manage.py lms --­­settings aws  create_user -e user@example.com

* set or change password ­

sudo -­u www-­data /edx/bin/python.edxapp ./manage.py lms ­­--settings aws changepassword user

* set user to staff ­

sudo ­-u www­-data /edx/bin/python.edxapp ./manage.py lms ­­--settings aws set_staff user@example.com

* launch the django shell ­

sudo -­u www­-data /edx/bin/python.edxapp ./manage.py lms ­­--settings aws shell

* create superuser ­

sudo ­u www-­data /edx/bin/python.edxapp ./manage.py lms ­­--settings aws create_user ­-e

user@example.com

sudo -­u www­-data /edx/bin/python.edxapp ./manage.py lms ­­--settings aws changepassword user

sudo -­u www­-data /edx/bin/python.edxapp ./manage.py lms ­­--settings aws shell

from django.contrib.auth.models import User

me = User.objects.get(username="user")

me.is_superuser = True

me.save()

* run migrations ­

sudo -­u www­-data /edx/bin/python.edxapp ./manage.py lms syncdb ­­ --migrate ­­ --settings aws

###8) Error in discern and ease requirement installation

**Sol**

a)sudo gedit /edx/app/discern/discern/requirements.txt

replace

­-e git+https://github.com/edx/ease.git@965a61b0701bb93bebdcd7173c3538008a1ea009#egg=ease

with

ease

c)sudo gedit /var/tmp/configuration/playbooks/roles/discern/tasks/deploy.yml

d) just comments following
```
 name: git checkout discern repo into discern_code_dir

 git: dest={{ discern_code_dir }} repo={{ discern_source_repo }} version={{ discern_version }}

```
### 9) Error in edxapp sync and migrate
```ruby
msg:ImportError: No module named dateutil.relativedelta
```
**Sol **
sudo pip install python­-dateutil

and then follow

a)Download python­-dateutil-­2.2.tar.gz (md5) from https://pypi.python.org/pypi/python­dateutil

b) extract and run command

cd /path of folder python­dateutil­2.2/ and

sudo python setup.py install

c) Copy the dateutils folder into /edx/app/ora/venvs/ora/local/lib/python2.7/site­packages/

d)copy folder python_dateutil­2.2­py2.7.egg­info and paste into

/edx/app/ora/venvs/ora/local/lib/python2.7/site­packages/celery/dateutil



### 10) Error in forum installation
```ruby
TASK: [forum | test that the required service are listening] ******************

failed: [localhost] => (item={'host': u'localhost', 'port': u'9200', 'service': 'elasticsearch'}) =>

{"elapsed": 30, "failed": true, "item": {"host": "localhost", "port": "9200", "service":

"elasticsearch"}}

msg: Timeout when waiting for localhost:9200
```
**sol**

sudo add­-apt­-repository ppa:webupd8team/java 

sudo apt-­get install oracle-­java7­-installer


###11)Error in installing Supervisior
```ruby
TASK: [supervisor | install supervisor in its venv] ***************************
failed: [localhost] => {"failed": true, "item": ""}
msg: Could not get output from /usr/local/bin/virtualenv --help: Traceback (most recent call last):
  File "/usr/local/bin/virtualenv", line 5, in <module>
    from pkg_resources import load_entry_point
  File "/usr/local/lib/python2.7/dist-packages/pkg_resources.py", line 2829, in <module>
    working_set = WorkingSet._build_master()
  File "/usr/local/lib/python2.7/dist-packages/pkg_resources.py", line 451, in _build_master
    return cls._build_from_requirements(__requires__)
  File "/usr/local/lib/python2.7/dist-packages/pkg_resources.py", line 464, in _build_from_requirements
    dists = ws.resolve(reqs, Environment())
  File "/usr/local/lib/python2.7/dist-packages/pkg_resources.py", line 639, in resolve
    raise DistributionNotFound(req)
pkg_resources.DistributionNotFound: virtualenv==1.7.1.2


FATAL: all hosts have already failed -- aborting

PLAY RECAP ********************************************************************
           to retry, use: --limit @/home/raeha/edx_sandbox.retry

localhost                  : ok=65   changed=5    unreachable=0    failed=1 
```
Sol

sudo  pip install virtualenv==1.7.1.2


