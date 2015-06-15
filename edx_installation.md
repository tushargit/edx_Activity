 EdX Installation on Ubuntu 12.04 
=======================================
* sudo apt-­get update ­-y
* sudo apt-­get upgrade ­-y
* reboot
* sudo apt-get install -y build-essential software-properties-common python-software-properties curl git-core libxml2-dev libxslt1-dev python-pip python-apt python-dev
* sudo pip install ­­--upgrade pip
* sudo pip install ­­--upgrade virtualenv
* cd /var/tmp
* git clone -­b release https://github.com/edx/configuration
* cd /var/tmp/configuration
* sudo pip install ­-r requirements.txt
* cd /var/tmp/configuration/playbooks
*  sudo ansible-playbook -c local ./edx_sandbox.yml -i "localhost,"

  OR

  sudo ansible­-playbook -­c local ./edx_sandbox.yml -­i “x.x.x.x,"

##  Issues in edX Ubuntu 12.04 Installation

###1) Error in creating database for edxapp:

```
TASK [edxlocal | create a database for edxapp]*****************************

msg: unable to connect, check login_user and login_password are correct, or alternatively check ~/.my.cnf contains credentials
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
```
TASK: [mongo | install mongo server and recommends] ***************************

msg: 'apt­-get install 'mongodb-­10gen=2.4.7' '
failed: E: There are problems and -­y was used

without –force-yes
```
**Sol**

a)sudo apt-­get remove mongodb­-clients

b)sudo apt­-get install mongodb-­10gen=2.4.7

###3)Error in installing npm:
```
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

OR
http://www.ubuntuupdates.org/package/core/precise/universe/base/nodejs-dev


click the package and install through Ubuntu Software Center by clicking install button

d)sudo apt­-get install npm


###4)Error in installing nltk

**Sol**

sudo /edx/app/ora/venvs/ora/bin/python -­m nltk.downloader ­-d /edx/var/ora/nltk_data all

###5)npm Error

```
npm ERR! install Couldn't read dependencies
npm ERR! Error: ENOENT, open 'C:\projects\Client-A\Live\package.json'
npm ERR! If you need help, you may report this log at:
npm ERR!     <http://github.com/isaacs/npm/issues>
npm ERR! or email it to:
npm ERR!     <npm-@googlegroups.com>

npm ERR! System Windows_NT 6.1.7601
npm ERR! command "C:\\Program Files (x86)\\nodejs\\\\node.exe" "C:\\Program Files (x86)\\nodejs\\node_modules\\npm\\bin\\npm-cli.js" "install"
npm ERR! cwd C:\projects\Client-A\Live\
npm ERR! node -v v0.8.22
npm ERR! npm -v 1.2.14
npm ERR! path C:\projects\Client-A\Live\package.json
npm ERR! code ENOENT
npm ERR! errno 34
npm ERR!
npm ERR! Additional logging details can be found in:
npm ERR!     C:\projects\Client-A\Live\npm-debug.log
npm ERR! not ok code 0
```
** Sol **

sudo apt-get purge nodejs npm

curl -sL https://deb.nodesource.com/setup | sudo bash -

sudo apt-get install -y nodejs

sudo apt­-get install npm

###6)Error in installing pika
```
msg:Command /usr/bin/git clone -­q git://github.com/pika/pika.git /edx/app/xqueue/venvs/xqueue/src/pika/

failed with error code 128 in None

Storing complete log in /root/.pip/pip.log

:stderr: fatal: unable to connect to github.com:
```
**Sol**

a)sudo gedit /edx/app/xqueue/xqueue/requirements.txt

replace
```
­-e git://github.com/pika/pika.git@a731347fc0#egg=pika

with

pika
```
b)sudo gedit /var/tmp/configuration/playbooks/roles/xqueue/tasks/deploy.yml

and comments the following
```
 Do A Checkout

 name: xqueue | git checkout xqueue repo into {{app_base_dir}}

 git: dest={{xqueue_code_dir}} repo={{xqueue_source_repo}} version={{xqueue_version}}
```

###7)Error in installing discern
```
IOError: Could not build the egg.

­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­­

Cleaning up...

Command python setup.py egg_info failed with error code 1 in /edx/app/discern/venvs/discern/build/MySQL-­python

Storing debug log for failure in /edx/app/discern/.pip/pip.log
```
**Sol**

a)cd /edx/app/discern

b)git clone https://github.com/defance/discern.git

Or

a)sudo gedit /var/tmp/configuration/playbooks/roles/discern/defaults/main.yml
```
changed the value of variable 'discern_source_repo' to 'https://github.com/defance/discern.git'
```
### 8) Internal Server Error in CMS running

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
```
from django.contrib.auth.models import User

me = User.objects.get(username="user")

me.is_superuser = True

me.save()
```

* run migrations ­

sudo -­u www­-data /edx/bin/python.edxapp ./manage.py lms syncdb ­­ --migrate ­­ --settings aws

###9) Error in discern and ease requirement installation

**Sol**

a)sudo gedit /edx/app/discern/discern/requirements.txt

replace
```
­-e git+https://github.com/edx/ease.git@965a61b0701bb93bebdcd7173c3538008a1ea009#egg=ease

with

ease
```
c)sudo gedit /var/tmp/configuration/playbooks/roles/discern/tasks/deploy.yml

 and comment following
```
 name: git checkout discern repo into discern_code_dir

 git: dest={{ discern_code_dir }} repo={{ discern_source_repo }} version={{ discern_version }}

```
### 10) Error in edxapp sync and migrate
```
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



### 11) Error in forum installation
```
TASK: [forum | test that the required service are listening] ******************

failed: [localhost] => (item={'host': u'localhost', 'port': u'9200', 'service': 'elasticsearch'}) =>

{"elapsed": 30, "failed": true, "item": {"host": "localhost", "port": "9200", "service":

"elasticsearch"}}

msg: Timeout when waiting for localhost:9200
```
**Sol**

sudo add­-apt­-repository ppa:webupd8team/java 

sudo apt-­get install oracle-­java7­-installer


###12)Error in installing Supervisior
```
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
**Sol**

sudo  pip install virtualenv==1.7.1.2


###13)Error in installing Mongodb
```
TASK: [mongo | install mongo server and recommends] ***************************

failed: [localhost] => {"failed": true, "item": ""}

stderr: dpkg: error processing /var/cache/apt/archives/mongodb-10gen_2.4.7_i386.deb (--unpack):

trying to overwrite '/usr/bin/mongo', which is also in package mongodb-clients 1:2.0.4-1ubuntu2.1

dpkg-deb: error: subprocess paste was killed by signal (Broken pipe)

Errors were encountered while processing:

/var/cache/apt/archives/mongodb-10gen_2.4.7_i386.deb

E: Sub-process /usr/bin/dpkg returned an error code (1)

stdout: Reading package lists...

Building dependency tree...

Reading state information...

The following packages were automatically installed and are no longer required:

language-pack-kde-en linux-headers-3.2.0-29 language-pack-kde-en-base

kde-l10n-engb linux-headers-3.2.0-29-generic-pae libkms1 libreadline5

openjdk-7-jre-lib

Use 'apt-get autoremove' to remove them.

The following NEW packages will be installed:

mongodb-10gen

0 upgraded, 1 newly installed, 0 to remove and 23 not upgraded.

Need to get 0 B/87.1 MB of archives.

After this operation, 221 MB of additional disk space will be used.

(Reading database ... 300377 files and directories currently installed.)

Unpacking mongodb-10gen (from .../mongodb-10gen_2.4.7_i386.deb) ...

msg: 'apt-get install 'mongodb-10gen=2.4.7' ' failed: dpkg: error processing /var/cache/apt/archives/mongodb-10gen_2.4.7_i386.deb (--unpack):

trying to overwrite '/usr/bin/mongo', which is also in package mongodb-clients 1:2.0.4-1ubuntu2.1

dpkg-deb: error: subprocess paste was killed by signal (Broken pipe)

Errors were encountered while processing:

/var/cache/apt/archives/mongodb-10gen_2.4.7_i386.deb

E: Sub-process /usr/bin/dpkg returned an error code (1)

FATAL: all hosts have already failed -- aborting

PLAY RECAP ********************************************************************

to retry, use: --limit @/home/ninad/edx_sandbox.retry

localhost : ok=54 changed=3 unreachable=0 failed=1
```

**Sol**

sudo dpkg --configure -a

sudo apt-get -f install

sudo apt-get purge mongodb mongodb-clients mongodb-server mongodb-dev

sudo apt-get purge mongodb-10gen

sudo apt-get autoremove


###14)Error in edxapp  gather assets
```
TASK: [edxapp | gather  static assets with rake] ******************************
failed: [localhost] => (item=lms) => {"changed": true, "cmd": "SERVICE_VARIANT=lms rake lms:gather_assets:aws ", "delta": "0:00:00.099276", "end": "2014-08-07 07:19:47.397273", "item": "lms", "rc": 1, "start": "2014-08-07 07:19:47.297997"}
stderr: rake aborted!
No Rakefile found (looking for: rakefile, Rakefile, rakefile.rb, Rakefile.rb)

(See full trace by running task with --trace)
failed: [localhost] => (item=cms) => {"changed": true, "cmd": "SERVICE_VARIANT=cms rake cms:gather_assets:aws ", "delta": "0:00:00.078862", "end": "2014-08-07 07:19:47.631337", "item": "cms", "rc": 1, "start": "2014-08-07 07:19:47.552475"}
stderr: rake aborted!
No Rakefile found (looking for: rakefile, Rakefile, rakefile.rb, Rakefile.rb)
```

**Sol**

sudo gedit /var/tmp/configuration/playbooks/roles/edxapp/tasks/service_variant_config.yml

**and relace**
```

# Gather assets using rake if possible

- name: gather {{ item }} static assets with rake
  shell: >
    SERVICE_VARIANT={{ item }} rake {{ item }}:gather_assets:aws
    executable=/bin/bash
    chdir={{ edxapp_code_dir }}
  sudo_user: "{{ edxapp_user }}"
  when: celery_worker is not defined and not devstack and item != "lms-preview"
  with_items: service_variants_enabled
  notify:
  - "restart edxapp"
  - "restart edxapp_workers"
  environment: "{{ edxapp_environment }}"
```


**with**
  ```
# Gather assets using paver if possible
- name: gather {{ item }} static assets with paver
shell: >
SERVICE_VARIANT={{ item }} paver update_assets {{ item }} --settings=aws
executable=/bin/bash
chdir={{ edxapp_code_dir }}
sudo_user: "{{ edxapp_user }}"
when: celery_worker is not defined and not devstack and item != "lms-preview"
with_items: service_variants_enabled
notify:
- "restart edxapp"
- "restart edxapp_workers"
environment: "{{ edxapp_environment }}"

```
###15)Error in Task[discern|install ease python package]
 ImportError: No module named numpy.distutils.core #18 
 
  File "setup.py", line 152, in <module>
    setup_package()
  File "setup.py", line 144, in setup_package
    from numpy.distutils.core import setup
ImportError: No module named numpy.distutils.core

**Sol**

sudo  pip install numpy==1.6.2

**Error on sudo /edx/bin/update edx-platform master**

###16) TASK: [edxapp | code sandbox | put sandbox apparmor profile in complain mode] 
failed: [localhost] => {"changed": true, "cmd": ["/usr/sbin/aa-complain", "/etc/apparmor.d/code.sandbox"], "delta": "0:00:00.189952", "end": "2014-06-09 12:44:21.808579", "item": "", "rc": 1, "start": "2014-06-09 12:44:21.618627"}
stdout: /etc/apparmor.d/code.sandbox does not exist, please double-check the path.

FATAL: all hosts have already failed -- aborting


apparmor its installed, where that file comes from ?

**Sol**

source /edx/app/edx_ansible/venvs/edx_ansible/bin/activate

cd /edx/app/edx_ansible/edx_ansible/playbooks/edx-east


###17)Error in fetching  ppa Launchpad
```
TASK: [common | Install python-pycurl] 
failed: [localhost] => {"failed": true, "item": "", "parsed": false}
invalid output was: SUDO-SUCCESS-carjtxxbnpiagqxqsigetrqpnhgptftr
Traceback (most recent call last):
  File "/home/tushar/.ansible/tmp/ansible-1431511340.3-114824604994462/apt", line 1464, in <module>
    main()
  File "/home/tushar/.ansible/tmp/ansible-1431511340.3-114824604994462/apt", line 404, in main
    cache.update()
  File "/usr/lib/python2.7/dist-packages/apt/deprecation.py", line 98, in deprecated_function
    return func(*args, **kwds)
  File "/usr/lib/python2.7/dist-packages/apt/cache.py", line 441, in update
    raise FetchFailedException(e)
apt.cache.FetchFailedException: W:Failed to fetch http://ppa.launchpad.net/webupd8team/jupiter/ubuntu/dists/oneiric/main/source/Sources  404  Not Found

, W:Failed to fetch http://ppa.launchpad.net/ferramroberto/java/ubuntu/dists/precise/main/source/Sources  404  Not Found
, W:Failed to fetch http://ppa.launchpad.net/ferramroberto/java/ubuntu/dists/precise/main/binary-amd64/Packages  404  Not Found
, W:Failed to fetch http://ppa.launchpad.net/ferramroberto/java/ubuntu/dists/precise/main/binary-i386/Packages  404  Not Found
, W:Failed to fetch http://ftp.iitm.ac.in/cran/bin/linux/ubuntu/lucid/Sources  404  Not Found
, W:Failed to fetch http://ftp.iitm.ac.in/cran/bin/linux/ubuntu/lucid/Packages  404  Not Found
, E:Some index files failed to download. They have been ignored, or old ones used instead.


FATAL: all hosts have already failed -- aborting
```

**Sol**

sudo apt-get install ppa-purge

sudo ppa-purge ppa:ferramroberto/java

sudo add-apt-repository ppa:webupd8team/java

sudo apt-get update








###18)Error in update (dpkg: error:failed to open package info)
```
TASK: [common | Install role-independent useful system packages] ************** 
failed: [localhost] => {"failed": true, "item": ""}
stderr: dpkg: error: failed to open package info file `/var/lib/dpkg/available' for reading: No such file or directory
E: Sub-process /usr/bin/dpkg returned an error code (2)

stdout: Reading package lists...
Building dependency tree...
Reading state information...
The following NEW packages will be installed:
  python-pip
0 upgraded, 1 newly installed, 0 to remove and 197 not upgraded.
Need to get 0 B/95.1 kB of archives.
After this operation, 399 kB of additional disk space will be used.

msg: 'apt-get install 'python-pip' ' failed: dpkg: error: failed to open package info file `/var/lib/dpkg/available' for reading: No such file or directory
E: Sub-process /usr/bin/dpkg returned an error code 
```
**Sol**
sudo mkdir -p /var/lib/dpkg/{updates,alternatives,info,parts,triggers}

sudo mkdir -p /var/lib/dpkg/avalable

sudo killall apt* software-center* dpkg sudo apt-get clean sudo apt-get update sudo apt-get purge wine1.4 ia32-libs-multiarch sudo apt-get upgrade

sudo apt-get update

sudo dpkg --clear-avail

sudo cp /var/lib/dpkg/available-old /var/lib/dpkg/available

sudo apt-get update

###19)Error in update supervisor configuration
```
TASK: [supervisor | update supervisor configuration] ************************** 
failed: [localhost] => {"changed": true, "cmd": "/edx/app/devpi/venvs/supervisor/bin/supervisorctl -c /edx/app/devpi/supervisor/supervisord.conf update ", "delta": "0:00:00.127061", "end": "2015-05-14 11:53:09.898837", "item": "", "rc": 2, "start": "2015-05-14 11:53:09.771776", "stdout_lines": ["error: <class 'socket.error'>, [Errno 2] No such file or directory: file: /usr/lib/python2.7/socket.py line: 224"]}
stdout: error: <class 'socket.error'>, [Errno 2] No such file or directory: file: /usr/lib/python2.7/socket.py line: 224

```
**Sol**

sudo service nginx stop

sudo service supervisor stop

sudo service supervisor.devpi stop

sudo pkill -u www-data




###20)Error in Elasticsearch (link creation for elasticsearch server restart)
```
..........................................................
..........................................................
dpkg: warning: files list file for package `libexempi3' missing, assuming package has no files currently installed.

dpkg: warning: files list file for package `libgwibber-gtk2' missing, assuming package has no files currently installed.

Configuration file `/etc/init.d/elasticsearch'
 ==> File on system created by you or by a script.
 ==> File also in package provided by package maintainer.
   What would you like to do about it ?  Your options are:
    Y or I  : install the package maintainer's version
    N or O  : keep your currently-installed version
      D     : show the differences between the versions
      Z     : start a shell to examine the situation
 The default action is to keep your current version.
*** elasticsearch (Y/I/N/O/D/Z) [default=N] ? dpkg: error processing elasticsearch (--install):
 EOF on stdin at conffile prompt
Errors were encountered while processing:
 elasticsearch
stdout: Selecting previously unselected package elasticsearch.
(Reading database ... 1085 files and directories currently installed.)
Unpacking elasticsearch (from .../tmp/elasticsearch-0.90.2.deb) ...
Setting up elasticsearch (0.90.2) ...

````
**Sol**

sudo dpkg --configure -a
[sudo] password for user: 
```
Setting up elasticsearch (0.90.2) ...

Configuration file `/etc/init.d/elasticsearch'
 ==> File on system created by you or by a script.
 ==> File also in package provided by package maintainer.
   What would you like to do about it ?  Your options are:
    Y or I  : install the package maintainer's version
    N or O  : keep your currently-installed version
      D     : show the differences between the versions
      Z     : start a shell to examine the situation
 The default action is to keep your current version.
 ```
*** elasticsearch (Y/I/N/O/D/Z) [default=N] ? Y
````
Installing new version of config file /etc/init.d/elasticsearch ...

Configuration file `/etc/default/elasticsearch'
 ==> File on system created by you or by a script.
 ==> File also in package provided by package maintainer.
   What would you like to do about it ?  Your options are:
    Y or I  : install the package maintainer's version
    N or O  : keep your currently-installed version
      D     : show the differences between the versions
      Z     : start a shell to examine the situation
 The default action is to keep your current version.
```
*** elasticsearch (Y/I/N/O/D/Z) [default=N] ? Y
```
Installing new version of config file /etc/default/elasticsearch ...

Configuration file `/etc/elasticsearch/logging.yml'
 ==> File on system created by you or by a script.
 ==> File also in package provided by package maintainer.
   What would you like to do about it ?  Your options are:
    Y or I  : install the package maintainer's version
    N or O  : keep your currently-installed version
      D     : show the differences between the versions
      Z     : start a shell to examine the situation
 The default action is to keep your current version.
 ````
*** logging.yml (Y/I/N/O/D/Z) [default=N] ? Y
```
Installing new version of config file /etc/elasticsearch/logging.yml ...

Configuration file `/etc/elasticsearch/elasticsearch.yml'
 ==> File on system created by you or by a script.
 ==> File also in package provided by package maintainer.
   What would you like to do about it ?  Your options are:
    Y or I  : install the package maintainer's version
    N or O  : keep your currently-installed version
      D     : show the differences between the versions
      Z     : start a shell to examine the situation
 The default action is to keep your current version.
 ```
*** elasticsearch.yml (Y/I/N/O/D/Z) [default=N] ? Y
```
Installing new version of config file /etc/elasticsearch/elasticsearch.yml ...
 * Starting ElasticSearch Server                          

```
###21)Error in sync and migrate

```
TASK: [edxapp | syncdb and migrate] *******************************************
failed: [localhost] => {"changed": true, "cmd": "SERVICE_VARIANT=lms /edx/app/edxapp/venvs/edxapp/bin/django-admin.py syncdb --migrate --noinput --settings=lms.envs.aws --pythonpath=/edx/app/edxapp/edx-platform ", "delta": "0:00:04.478969", "end": "2015-05-18 16:10:49.164643", "item": "", "rc": 1, "start": "2015-05-18 16:10:44.685674"}
stderr: Traceback (most recent call last):
  File "/edx/app/edxapp/venvs/edxapp/bin/django-admin.py", line 5, in <module>
    management.execute_from_command_line()
  .................................................................................
  .................................................................................
  .................................................................................
  .................................................................................
_mysql_exceptions.OperationalError: (1045, "Access denied for user 'root'@'localhost' (using password: NO)")

FATAL: all hosts have already failed -- aborting





```
**Sol**

Change mysql username password setting in following way:
On MySQL 

mysql> update user set password=PASSWORD('') where User='root';

mysql> flush privileges;


###22)TASK: [edxapp | install python base-requirements] 

```
"failed: [localhost] => {"changed": true, "cmd": "/edx/app/edxapp/venvs/edxapp/bin/pip install -i https://pypi.python.org/simple --exists-action w --use-mirrors -r /edx/app/edxapp/edx-platform/requirements/edx/base.txt ", "delta": "0:00:44.433877", "end": "2014-09-30 07:08:45.484337", "item": "", "rc": 1, "start": "2014-09-30 07:08:01.050460"}
stdout: --use-mirrors has been deprecated and will be removed in the future. Explicit uses of --index-url and/or --extra-index-url is suggested.
................................................................................................

................................................................................................


x86_64-linux-gnu-gcc -pthread -fno-strict-aliasing -DNDEBUG -g -fwrapv -O2 -Wall -Wstrict-prototypes -fPIC -I/usr/include/freetype2 -IlibImaging -I/edx/app/edxapp/venvs/edxapp/include -I/usr/local/include -I/usr/include -I/usr/include/python2.7 -I/usr/include/x86_64-linux-gnu -c _imagingft.c -o build/temp.linux-x86_64-2.7/_imagingft.o
_imagingft.c:73:31: fatal error: freetype/fterrors.h: No such file or directory
#include
^
compilation terminated.
error: command 'x86_64-linux-gnu-gcc' failed with exit status 1
Complete output from command /edx/app/edxapp/venvs/edxapp/bin/python -c "import setuptools, tokenize;__file='/edx/app/edxapp/venvs/edxapp/build/Pillow/setup.py';exec(compile(getattr(tokenize, 'open', open)(file).read().replace('\r\n', '\n'), file, 'exec'))" install --record /tmp/pip-jLnUps-record/install-record.txt --single-version-externally-managed --compile --install-headers /edx/app/edxapp/venvs/edxapp/include/site/python2.7:
running install

running build

running build_py

creating build

creating build/lib.linux-x86_64-2.7
...................................................................................................

...................................................................................................

x86_64-linux-gnu-gcc -pthread -fno-strict-aliasing -DNDEBUG -g -fwrapv -O2 -Wall -Wstrict-prototypes -fPIC -I/usr/include/freetype2 -IlibImaging -I/edx/app/edxapp/venvs/edxapp/include -I/usr/local/include -I/usr/include -I/usr/include/python2.7 -I/usr/include/x86_64-linux-gnu -c _imagingft.c -o build/temp.linux-x86_64-2.7/_imagingft.o

_imagingft.c:73:31: fatal error: freetype/fterrors.h: No such file or directory

#include

                           ^

compilation terminated.

error: command 'x86_64-linux-gnu-gcc' failed with exit status 1

Cleaning up...
Command /edx/app/edxapp/venvs/edxapp/bin/python -c "import setuptools, tokenize;file='/edx/app/edxapp/venvs/edxapp/build/Pillow/setup.py';exec(compile(getattr(tokenize, 'open', open)(file).read().replace('\r\n', '\n'), file, 'exec'))" install --record /tmp/pip-jLnUps-record/install-record.txt --single-version-externally-managed --compile --install-headers /edx/app/edxapp/venvs/edxapp/include/site/python2.7 failed with error code 1 in /edx/app/edxapp/venvs/edxapp/build/Pillow
Storing debug log for failure in /edx/app/edxapp/.pip/pip.log

FATAL: all hosts have already failed -- aborting

PLAY RECAP ********************************************************************
to retry, use: --limit @/home/ubuntu/edx_sandbox.retry

localhost : ok=159 changed=25 unreachable=0 failed=1 
```

**Sol**

freetype2 is there instead of freetype so, make symlink..

sudo ln -s /usr/include/freetype2 /usr/include/freetype

###23)Error in installing  python post-post requirements
```
...............................................................................

................................................................................
stderr: /bin/sh: 1: /edx/app/edxapp/venvs/edxapp/bin/pip: not found
................................................................................

................................................................................
```
**Sol**

sudo gedit /var/tmp/iitbx-configuration/playbooks/roles/edxapp/tasks/deploy.yml
replace code
```
# Install the final python modules into {{ edxapp_venv_dir }}
- name : install python post-post requirements
  # Need to use shell rather than pip so that we can maintain the context of our current working directory; some
  # requirements are pathed relative to the edx-platform repo. Using the pip from inside the virtual environment implicitly
  # installs everything into that virtual environment.
  shell: >
    {{ edxapp_venv_dir }}/bin/pip install -i {{ edxapp_pypi_local_mirror }} --exists-action w --use-mirrors -r {{ item }}
    chdir={{ edxapp_code_dir }}
  with_items:
  - "{{ repo_requirements_file }}"
  - "{{ github_requirements_file }}"
  - "{{ local_requirements_file }}"
  sudo_user: "{{ edxapp_user }}"
  notify:
  - "restart edxapp"
  - "restart edxapp_workers"
  ```
  
 with
  ```
# Install the final python modules into {{ edxapp_venv_dir }}
- name : install python post-post requirements
  # Need to use shell rather than pip so that we can maintain the context of our current working directory; some
  # requirements are pathed relative to the edx-platform repo. Using the pip from inside the virtual environment implicitly
  # installs everything into that virtual environment.
  shell: >
    {{ edxapp_venv_dir }}/bin/pip install -i {{ edxapp_pypi_local_mirror }} --exists-action w --use-mirrors -r {{ item }}
    chdir={{ edxapp_code_dir }}
  with_items:
  - "{{ repo_requirements_file }}"
  - "{{ github_requirements_file }}"
  - "{{ local_requirements_file }}"
  sudo_user: "{{ edxapp_user }}"
  notify:
  - "restart edxapp"
  - "restart edxapp_workers"
  when: not inst.stat.exists or new.stat.md5 != inst.stat.md5

```
###24)Error in Bower
```
............................................................................
............................................................................
bower globalize#1.0.0-alpha.17                                    not-cached git://github.com/jquery/globalize.git#1.0.0-alpha.17
bower globalize#1.0.0-alpha.17                                       resolve git://github.com/jquery/globalize.git#1.0.0-alpha.17
bower globalize#1.0.0-alpha.17                                       resolve git://github.com/jquery/globalize.git#1.0.0-alpha.17
bower natural-sort#dbf4ca259b327a488bd1d7897fd46d80c414a7e0          ECMDERR Failed to execute "git clone git://github.com/overset/javascript-natural-sort.git /tmp/tushar/bower/natural-sort-15515-Knfiii --progress", exit code of #128 fatal: unable to connect to github.com: github.com[0: 192.30.252.129]: errno=Connection timed out

Additional error details:
fatal: unable to connect to github.com:
github.com[0: 192.30.252.129]: errno=Connection timed out
make: *** [requirements.js] Error 1
```
**Sol** 

Paste on terminal

git config --global url."https://".insteadOf git://

sudo /edx/bin/ansible-playbook -i localhost, -c local edxapp.yml -e 'edx_platform_version=master'


#Note:
After Update from above command ,lms and cms can access by following url:

For LMS: http://localhost:18000/
For CMS:http://localhost:18010/

