Standford theme configuration in edx
====================================
* cd  /edx/app/edxapp

* sudo gedit  lms.envs.json
```
...............
..............

"USE_CUSTOM_THEME": true
...............
...............
"THEME_NAME": "THEMENAME", 
...............
..............
```
* mkdir themes

* cd themes

  git clone https://github.com/Stanford-Online/edx-theme.git

* rename it to THEMENAME

* cd  THEMENAME/static/images
 
  paste images

* cd  THEMENAME/static/sass

  rename _default.scss to THEMENAME.scss

* sudo gedit THEMENAME.scss
```
...................
...................
$homepage-bg-image: url('../themes/THEMENAME/images/image.jpg');

.................
.................

$video-thumb-url: '../themes/THEMENAME/images/image2.png';
....................
....................
```
* source /edx/app/edxapp/edxapp_env 

* sudo -u edxapp -E PATH="/edx/app/edxapp/venvs/edxapp/bin:/edx/app/edxapp/edx-platform/bin:/edx/app/edxapp/.rbenv/bin:/edx/app/edxapp/.rbenv/shims:/edx/app/edxapp/.gem/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin" SERVICE_VARIANT=lms /edx/app/edxapp/edx-platform/bin/rake lms:gather_assets:aws 

* sudo chmod 777 -R /edx/var/edxapp/staticfiles/themes/
