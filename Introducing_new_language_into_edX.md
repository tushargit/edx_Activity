Introducing_new_language_into_edX
=================================

Considering creation of Assames language:

#. create a python virtualenv and install all of the python requirements

  cd /edx/app/edxapp/edx-platform/
  pip install --exists-action w -r requirements/edx/pre.txt
  pip install --exists-action w -r requirements/edx/base.txt
  pip install --exists-action w -r requirements/edx/post.txt
  pip install --exists-action w -r requirements/edx/repo.txt
  pip install --exists-action w -r requirements/edx/github.txt
  pip install --exists-action w -r requirements/edx/local.txt
  pip install markey
  
#. extract the .po source files

Below command will extract all the strings from files(*.py,*.html) into *.po files of /edx/app/edxapp/edx-platform/conf/locale/en/LC_MESSAGES/
If *.po files are there,the below command will override the *.po files with the extra strings that are added(i.e. in *.py, *.html files ) and if there is no *.po files or you have deleted them then also it will create *.po files.

  	i18n_tool extract

#. create an language code directory

	considering *'as'* as the language code for Assames language

  	cd /edx/app/edxapp/edx-platform/conf/locale/
  	mkdir as/

#.Copy all the files from 'en' folder into 'as' folder

  	cp -r en/* as/

#. Update the .po files in the as/ directory for you translation strings

  	.....................
  	.....................
  	#: common/djangoapps/django_comment_common/models.py
  	msgid "Moderator"
  	msgstr "मॉडरेटर"
  	.....................
  	.....................
  
#. Add the language into /edx/app/edxapp/edx-platform/conf/locale/config.yaml 

  	- as  # Assames

#. Add the language into /edx/app/edxapp/edx-platform/lms/envs/common.py

	LANGUAGES = (
    		('en', u'English'),
    		('am', u'አማርኛ'), 
		('as' , u' বাতিল'),  #Assames

#. Make changes to django-partial.po,djangojs-partial.po,mako.po by replacing 'en' to 'as'

  	BEFORE: "Language: en\n"
	AFTER: "Language: as\n”

#. Generate the compiled files
		
		i18n_tool generate
The above command will generate *.mo files

#. Add "as" to your list of supported languages
	
	Goto [IP]/admin/dark_lang/darklangconfig/
	Click on the Add and edit button to add the language code.
	Save it.
	
#. Sometimes the changes wount reflect so start the lms and cms:
  	cd /edx/app/edxapp/edx-platform
  	sudo /edx/bin/supervisorctl -c /edx/etc/supervisord.conf restart edxapp:
  	
  
Refresh the page to see the changes in the browser.There is a delay of 2.5 mins on the main page but on other pages it reflects.

  
	
	


