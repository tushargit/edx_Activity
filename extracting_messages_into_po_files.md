 Extracting_messages_into_po_files/Creating_Po_files
=====================================================

* sudo gedit  /edx/app/edxapp/edx-platform/lms/envs/common.py

           LANGUAGES = (
                                        ..............................
                                        ..............................
                                       ('ks', u'kashmiri'),  # Kashmiri
                                       ...............................
                                       ...............................
                        )
                                        

* sudo gedit   /edx/app/edxapp/edx-platform/conf/locale/config.yaml
     

          locales:
                     - kn  # Kannada 
                     -ks #kashmiri

* cd  /edx/app/edxapp/edx-platform/conf/locale/
* mkdir ks
* cd ks 
* mkdir LC_MESSAGES
* sudo gedit /edx/app/edxapp/venvs/edxapp/src/i18n-tools/i18n/extract.py


   replace 'en' with 'language_code' from following place 
   
   eg.
   
                 makemessages = "django-admin.py makemessages -l en -v{}".format(args.verbose)
                 
                  fixes ={
                 
                              .............................
                              'Language': 'en',
                               ...........................
                 
                              }
                 
                  segmented_files = segment_pofiles("en")               
 


     
* sudo gedit  /edx/app/edxapp/venvs/edxapp/src/i18n-tools/i18n/config.py

         replace 'en' with 'language_code' from following place 

                DEFAULTS = { 
             'dummy_locales': [], 
             'generate_merge': {}, 
             'ignore_dirs': [], 
             'locales': ['en'], 
             'segment': {}, 
             'source_locale': 'en', 
             'third_party': [], 
              } 
       

* run on terminal
   i18n_tool extract

