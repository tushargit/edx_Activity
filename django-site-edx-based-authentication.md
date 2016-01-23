Login in Django website   using EdX authentication(Login) on ubuntu  
===================================================================
* pip install  edx-auth-backends
* Modification on lms server 
 - Go to lms admin interface and select oauth2 client 
   <img src='https://github.com/tushargit/edx_Activity/blob/master/lmsoauth.png' width="600px" height="400px" />
   
 - Fill user and replace mysite.com with your site url or IP if running on Ip as given below. 
   <img src='https://github.com/tushargit/edx_Activity/blob/master/lmsoauthclientcreate.png' width="600px" height="400px" />
   
 - Select trusted client from admin interface.
   <img src='https://github.com/tushargit/edx_Activity/blob/master/lmsoauthadmin2.png' width="600px" height="400px" />
   
 - Add your client from dropdown to trusted client .
   <img src='https://github.com/tushargit/edx_Activity/blob/master/lmsoauthaddtrustedclient.png' width="600px" height="400px" />
   
* sudo vi  /edx/app/edxapp/lms.env.json
```
"FEATURES": { 
        "ENABLE_OAUTH2_PROVIDER": true, 
        "AUTH_USE_OPENID_PROVIDER": true,
     }

"JWT_ISSUER": "http://lms.org/oauth2",

 "OAUTH_ENFORCE_SECURE": false, 
 "OAUTH_OIDC_ISSUER": "http://lms.org/oauth2", 

```
* sudo vi /edx/app/edxapp/lms/envs/common.py
```
'AUTH_USE_OPENID': True, 
 'AUTH_USE_OPENID_PROVIDER': True,
'ENABLE_OAUTH2_PROVIDER': True,
OAUTH_OIDC_ISSUER = 'http://127.0.0.1:8000/oauth2' 
```
   

* sudo vi /{PROJECTDIR}/{PROJECT}/{PROJECT}/settings.py
```
INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app1',
    'app2',
    'auth_backends',
)

INSTALLED_APPS += ('social.apps.django_app.default',)

# Allow authentication via edX OAuth
AUTHENTICATION_BACKENDS = (
    'auth_backends.backends.EdXOpenIdConnect',
    'django.contrib.auth.backends.ModelBackend',
)


# Set to true if using SSL and running behind a proxy
SOCIAL_AUTH_REDIRECT_IS_HTTPS = False
SOCIAL_AUTH_ADMIN_USER_SEARCH_FIELDS = ['username', 'email']
SOCIAL_AUTH_PIPELINE = (
'social.pipeline.social_auth.social_details',
'social.pipeline.social_auth.social_uid',
'social.pipeline.social_auth.auth_allowed',
'social.pipeline.social_auth.social_user',
# By default python-social-auth will simply create a new user/username if the username
# from the provider conflicts with an existing username in this system. This custom pipeline function
# loads existing users instead of creating new ones.
'auth_backends.pipeline.get_user_if_exists',
'social.pipeline.user.get_username',
'social.pipeline.user.create_user',
'social.pipeline.social_auth.associate_user',
'social.pipeline.social_auth.load_extra_data',
'social.pipeline.user.user_details'
)
SOCIAL_AUTH_USER_FIELDS = ['username', 'email', 'first_name', 'last_name']

SOCIAL_AUTH_RAISE_EXCEPTIONS = True

OAUTH2_PROVIDER_URL = 'http://lms.org/oauth2'

SOCIAL_AUTH_EDX_OIDC_URL_ROOT = OAUTH2_PROVIDER_URL
SOCIAL_AUTH_EDX_OIDC_KEY = 'CLIENT ID COPIED FROM OAUTH2 LMS'
SOCIAL_AUTH_EDX_OIDC_SECRET = 'CLIENT SECRET COPIED FROM OAUTH2 LMS'
SOCIAL_AUTH_EDX_OIDC_ID_TOKEN_DECRYPTION_KEY = SOCIAL_AUTH_EDX_OIDC_SECRET
LOGIN_REDIRECT_URL = '/'

```
* python manage.py migrate
