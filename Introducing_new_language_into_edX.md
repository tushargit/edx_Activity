Introducing_new_language_into_edX
=================================

Considering creation of Assames language:

1. create a python virtualenv and install all of the python requirements
````````````````````````````````````````````````````````````````````````

	cd /edx/app/edxapp/edx-platform/
	pip install --exists-action w -r requirements/edx/pre.txt
	pip install --exists-action w -r requirements/edx/base.txt
	pip install --exists-action w -r requirements/edx/post.txt
	pip install --exists-action w -r requirements/edx/repo.txt
	pip install --exists-action w -r requirements/edx/github.txt
	pip install --exists-action w -r requirements/edx/local.txt
	pip install markey


2. Extract the .po source files
```````````````````````````````

Below command will extract all the strings from files(*.py,*.html) into *.po files of /edx/app/edxapp/edx-platform/conf/locale/en/LC_MESSAGES/
If *.po files are there,the below command will override the *.po files with the extra strings that are added(i.e. in *.py, *.html files ) and if there is no *.po files or you have deleted them then also it will create *.po files.

	i18n_tool extract

3. create an language code director
```````````````````````````````````

	considering *'as'* as the language code for Assames language

	cd /edx/app/edxapp/edx-platform/conf/locale/
	mkdir as/

4. Copy all the files from 'en' folder into 'as' folder
```````````````````````````````````````````````````````

	cp -r en/* as/

5. Update the .po files in the as/ directory for you translation strings
`````````````````````````````````````````````````````````````````````````

	.....................
  	.....................
  	#: common/djangoapps/django_comment_common/models.py
  	msgid "Moderator"
  	msgstr "मॉडरेटर"
  	.....................
  	.....................
  
6. Add the language into /edx/app/edxapp/edx-platform/conf/locale/config.yaml
`````````````````````````````````````````````````````````````````````````````


	 as  # Assames

7. Add the language into /edx/app/edxapp/edx-platform/lms/envs/common.py
`````````````````````````````````````````````````````````````````````````

	LANGUAGES = (
    		('en', u'English'),
    		('am', u'አማርኛ'), 
		('as' , u' বাতিল'),  #Assames

8. Make changes to django-partial.po,djangojs-partial.po,mako.po by replacing 'en' to 'as'
`````````````````````````````````````````````````````````````````````````````````````````

  	BEFORE: "Language: en\n"
	AFTER: "Language: as\n”

9. Generate the compiled files
```````````````````````````````

	i18n_tool generate
The above command will generate *.mo files

10. Add "as" to your list of supported languages
````````````````````````````````````````````````

	Goto [IP]/admin/dark_lang/darklangconfig/
	Click on the Add and edit button to add the language code.
	Save it.
	
11. Sometimes the changes wount reflect so start the lms and cms:
`````````````````````````````````````````````````````````````````

	cd /edx/app/edxapp/edx-platform
  	sudo /edx/bin/supervisorctl -c /edx/etc/supervisord.conf restart edxapp:
  	
  
Refresh the page to see the changes in the browser.There is a delay of 2.5 mins on the main page but on other pages it reflects.

  
	
	


