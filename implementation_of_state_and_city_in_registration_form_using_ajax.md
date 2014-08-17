Adding state and city field in registration form using ajax
============================================================

* **sudo gedit  /edx/app/edxapp/edx-platform/common/djangoapps/student/models.py**
```
class mooc_state(models.Model):      

      name=models.CharField(max_length=255,unique=True,db_index=True,null=False,default=None)

     

      def __unicode__(self):

          return self.name





class mooc_city(models.Model):

     

      name= models.CharField(max_length=255,null=False,default=None)

      state = models.ForeignKey(mooc_state)

      def __unicode__(self):

          return self.name

          

class mooc_person(models.Model):

      user = models.OneToOneField(User,null=False,default=None)

      state = models.ForeignKey(mooc_state,default=None)

      city=models.ForeignKey(mooc_city,default=None)

      pincode = models.IntegerField(max_length=25,default=None)

      
      def __unicode__(self):

          return self.user_id.username    

```




* sudo -u www-data /edx/bin/python.edxapp ./manage.py lms schemamigration student --initial --settings aws

* sudo -u www-data /edx/bin/python.edxapp ./manage.py lms syncdb --all --settings aws

* sudo -u www-data /edx/bin/python.edxapp ./manage.py lms migrate --fake --settings aws

* Insert data in student_mooc_state and student_mooc_city table of edxapp database  

### To Populate data from database in state dropdown: ###

*  sudo gedit /edx/app/edxapp/edx-platform/lms/envs/common.py and add state as following
```
    REGISTRATION_EXTRA_FIELDS = {
    'level_of_education': 'optional',
    'gender': 'optional',
    'year_of_birth': 'optional',
    'mailing_address': 'optional',
    'goals': 'optional',
    'honor_code': 'required',
    #'city': 'hidden',
    'country': 'hidden',
    'state':'required',
}
```

* sudo gedit  /edx/app/edxapp/edx-platform/lms/templates/register.html 
```
<!--##########  Added state dropdown  ############################### -->

     % if settings.REGISTRATION_EXTRA_FIELDS['state'] != 'hidden':

     <li class="field-group">

    <div class="field ${settings.REGISTRATION_EXTRA_FIELDS['state']} select" id="field-state-level">

     <label for="state-level">${_("state")}</label>

       <select id="state-level" name="state" ${'required aria-required="true"' if settings.REGISTRATION_EXTRA_FIELDS['state'] == 'required' else ''}>

                <option value="">--</option>

                %for state in states:                

                <option value="{{state.id}}">${ unicode(state.name) }</option>

                %endfor

              </select>

            </div>

          </li>

          % endif
```

* sudo gedit /edx/app/edxapp/edx-platform/common/djangoapps/student/views.py as following

```
Change No 1:
from student.models import (
    Registration, UserProfile, PendingNameChange,
    PendingEmailChange, CourseEnrollment, unique_id_for_user,
    CourseEnrollmentAllowed, UserStanding, LoginFailures,        
create_comments_service_user, PasswordHistory,Mooc_state,Mooc_city,Mooc_person 
)
 Change NO 2:
   for field_name in required_post_vars: 
        if field_name in ('gender', 'level_of_education','state'): 
            min_length = 1 
        else: 
            min_length = 2 
        if len(post_vars[field_name]) < min_length: 
            error_str = { 
                'username': _('Username must be minimum of two characters long'), 
                'email': _('A properly formatted e-mail is required'), 
                'name': _('Your legal name must be a minimum of two characters long'), 
                'password': _('A valid password is required'), 
                'terms_of_service': _('Accepting Terms of Service is required'), 
                'honor_code': _('Agreeing to the Honor Code is required'), 
                'level_of_education': _('A level of education is required'), 
                'gender': _('Your gender is required'), 
                'year_of_birth': _('Your year of birth is required'), 
                'mailing_address': _('Your mailing address is required'), 
                'goals': _('A description of your goals is required'), 
                'city': _('A city is required'), 
                'country': _('A country is required'), 
                'state':_('State is required') 


            }

change NO 3: Change the register_user function 
@ensure_csrf_cookie

def register_user(request, extra_context=None):

    """

    This view will display the non-modal registration form

    """

    states=Mooc_state.objects.all() 

    city = Mooc_city.objects.all() 



    if request.user.is_authenticated():

        return redirect(reverse('dashboard'))

    if settings.FEATURES.get('AUTH_USE_CERTIFICATES_IMMEDIATE_SIGNUP'):

        # Redirect to branding to process their certificate if SSL is enabled

        # and registration is disabled.

        return redirect(reverse('root'))


    context = {'states':states,

        'cities':city,

        'course_id': request.GET.get('course_id'),

        'enrollment_action': request.GET.get('enrollment_action'),

        'platform_name': microsite.get_value(

            'platform_name',

            settings.PLATFORM_NAME

        ),

    }

    if extra_context is not None:

        context.update(extra_context)



    if context.get("extauth_domain", '').startswith(external_auth.views.SHIBBOLETH_DOMAIN_PREFIX):

        return render_to_response('register-shib.html', context)

    return render_to_response('register.html', context)

```
* sudo gedit  /edx/app/edxapp/edx-platform/common/djangoapps/student/views.py for using city dropdownlist   
```

changes 4: this is a new function caled by AJAX to acces states based on the selected state id.
@ensure_csrf_cookie

def city_ajax(request):

    if request.is_ajax() and request.method =='POST':

       states = request.POST.get('id')

       city = Mooc_city.objects.filter(state_id=states)

    data = serializers.serialize('json', city)

    return HttpResponse(json.dumps(data), mimetype="application/json")

```
*  Edit  views.py for inserting state ,city and pincode in sql database
```
change NO: 5 change the _do_create_account function
def _do_create_account(post_vars):

    """

    Given cleaned post variables, create the User and UserProfile objects, as well as the

    registration for this user.



    Returns a tuple (User, UserProfile, Registration ,Mooc_person).



    Note: this function is also used for creating test users.

    """

    user = User(username=post_vars['username'],

                email=post_vars['email'],

                is_active=False)

    user.set_password(post_vars['password'])

    registration = Registration()



    # TODO: Rearrange so that if part of the process fails, the whole process fails.

    # Right now, we can have e.g. no registration e-mail sent out and a zombie account

    try:

        user.save()

    except IntegrityError:

        # Figure out the cause of the integrity error

        if len(User.objects.filter(username=post_vars['username'])) > 0:

            raise AccountValidationError(

                _("An account with the Public Username '{username}' already exists.").format(username=post_vars['username']),

                field="username"

                )

        elif len(User.objects.filter(email=post_vars['email'])) > 0:

            raise AccountValidationError(

                _("An account with the Email '{email}' already exists.").format(email=post_vars['email']),

                field="email"

                )

        else:

            raise



    # add this account creation to password history

    # NOTE, this will be a NOP unless the feature has been turned on in configuration

    password_history_entry = PasswordHistory()

    password_history_entry.create(user)



    registration.register(user)



    profile = UserProfile(user=user)

    mooc_person = Mooc_person(user=user) 
    profile.name = post_vars['name']

    profile.level_of_education = post_vars.get('level_of_education')

    profile.gender = post_vars.get('gender')

    profile.mailing_address = post_vars.get('mailing_address')

    #profile.city = post_vars.get('city')

    #profile.country = post_vars.get('country')
 
    profile.goals = post_vars.get('goals')

    mooc_person.state_id=post_vars.get('state')

    mooc_person.city_id = post_vars.get('city')





    try:

        profile.year_of_birth = int(post_vars['year_of_birth'])

    except (ValueError, KeyError):

        # If they give us garbage, just ignore it instead

        # of asking them to put an integer.

        profile.year_of_birth = None

    try:

        profile.save()

        mooc_person.save()

    except Exception:

        log.exception("UserProfile creation failed for user {id}.".format(id=user.id))

        raise



    UserPreference.set_preference(user, LANGUAGE_KEY, get_language())



    return (user, profile, registration,mooc_person)


change : 6  change the create account function
@ensure_csrf_cookie

def create_account(request, post_override=None):

    """

    JSON call to create new edX account.

    Used by form in signup_modal.html, which is included into navigation.html

    """

    js = {'success': False}



    post_vars = post_override if post_override else request.POST

    extra_fields = getattr(settings, 'REGISTRATION_EXTRA_FIELDS', {})



    # if doing signup for an external authorization, then get email, password, name from the eamap

    # don't use the ones from the form, since the user could have hacked those

    # unless originally we didn't get a valid email or name from the external auth

    DoExternalAuth = 'ExternalAuthMap' in request.session

    if DoExternalAuth:

        eamap = request.session['ExternalAuthMap']

        try:

            validate_email(eamap.external_email)

            email = eamap.external_email

        except ValidationError:

            email = post_vars.get('email', '')

        if eamap.external_name.strip() == '':

            name = post_vars.get('name', '')

        else:

            name = eamap.external_name

        password = eamap.internal_password

        post_vars = dict(post_vars.items())

        post_vars.update(dict(email=email, name=name, password=password))

        log.debug(u'In create_account with external_auth: user = %s, email=%s', name, email)



    # Confirm we have a properly formed request

    for a in ['username', 'email', 'password', 'name']:

        if a not in post_vars:

            js['value'] = _("Error (401 {field}). E-mail us.").format(field=a)

            js['field'] = a

            return JsonResponse(js, status=400)



    if extra_fields.get('honor_code', 'required') == 'required' and \

            post_vars.get('honor_code', 'false') != u'true':

        js['value'] = _("To enroll, you must follow the honor code.").format(field=a)

        js['field'] = 'honor_code'

        return JsonResponse(js, status=400)



    # Can't have terms of service for certain SHIB users, like at Stanford

    tos_required = (

        not settings.FEATURES.get("AUTH_USE_SHIB") or

        not settings.FEATURES.get("SHIB_DISABLE_TOS") or

        not DoExternalAuth or

        not eamap.external_domain.startswith(

            external_auth.views.SHIBBOLETH_DOMAIN_PREFIX

        )

    )



    if tos_required:

        if post_vars.get('terms_of_service', 'false') != u'true':

            js['value'] = _("You must accept the terms of service.").format(field=a)

            js['field'] = 'terms_of_service'

            return JsonResponse(js, status=400)



    # Confirm appropriate fields are there.

    # TODO: Check e-mail format is correct.

    # TODO: Confirm e-mail is not from a generic domain (mailinator, etc.)? Not sure if

    # this is a good idea

    # TODO: Check password is sane



    required_post_vars = ['username', 'email', 'name', 'password']

    required_post_vars += [fieldname for fieldname, val in extra_fields.items()

                           if val == 'required']

    if tos_required:

        required_post_vars.append('terms_of_service')



    for field_name in required_post_vars:

        if field_name in ('gender', 'level_of_education','state'):         
            min_length = 1

        else:

            min_length = 2



        if len(post_vars[field_name]) < min_length:

            error_str = {

                'username': _('Username must be minimum of two characters long'),

                'email': _('A properly formatted e-mail is required'),

                'name': _('Your legal name must be a minimum of two characters long'),

                'password': _('A valid password is required'),

                'terms_of_service': _('Accepting Terms of Service is required'),

                'honor_code': _('Agreeing to the Honor Code is required'),

                'level_of_education': _('A level of education is required'),

                'gender': _('Your gender is required'),

                'year_of_birth': _('Your year of birth is required'),

                'mailing_address': _('Your mailing address is required'),

                'goals': _('A description of your goals is required'),

                'city': _('A city is required'),                             

                'country': _('A country is required'),

                'state':_('State is required'),                              
                                

            }

            js['value'] = error_str[field_name]

            js['field'] = field_name

            return JsonResponse(js, status=400)



        max_length = 75

        if field_name == 'username':

            max_length = 30



        if field_name in ('email', 'username') and len(post_vars[field_name]) > max_length:

            error_str = {

                'username': _('Username cannot be more than {0} characters long').format(max_length),

                'email': _('Email cannot be more than {0} characters long').format(max_length)

            }

            js['value'] = error_str[field_name]

            js['field'] = field_name

            return JsonResponse(js, status=400)



    try:

        validate_email(post_vars['email'])

    except ValidationError:

        js['value'] = _("Valid e-mail is required.").format(field=a)

        js['field'] = 'email'

        return JsonResponse(js, status=400)



    try:

        validate_slug(post_vars['username'])

    except ValidationError:

        js['value'] = _("Username should only consist of A-Z and 0-9, with no spaces.").format(field=a)

        js['field'] = 'username'

        return JsonResponse(js, status=400)



    # enforce password complexity as an optional feature

    if settings.FEATURES.get('ENFORCE_PASSWORD_POLICY', False):

        try:

            password = post_vars['password']



            validate_password_length(password)

            validate_password_complexity(password)

            validate_password_dictionary(password)

        except ValidationError, err:

            js['value'] = _('Password: ') + '; '.join(err.messages)

            js['field'] = 'password'

            return JsonResponse(js, status=400)



    # Ok, looks like everything is legit.  Create the account.

    try:

        with transaction.commit_on_success():

            ret = _do_create_account(post_vars)

    except AccountValidationError as e:

        return JsonResponse({'success': False, 'value': e.message, 'field': e.field}, status=400)



    (user, profile, registration,mooc_user) = ret



    dog_stats_api.increment("common.student.account_created")

    create_comments_service_user(user)



    context = {

        'name': post_vars['name'],

        'key': registration.activation_key,

    }



    # composes activation email

    subject = render_to_string('emails/activation_email_subject.txt', context)

    # Email subject *must not* contain newlines

    subject = ''.join(subject.splitlines())

    message = render_to_string('emails/activation_email.txt', context)



    # don't send email if we are doing load testing or random user generation for some reason

    if not (settings.FEATURES.get('AUTOMATIC_AUTH_FOR_TESTING')):

        from_address = microsite.get_value(

            'email_from_address',

            settings.DEFAULT_FROM_EMAIL

        )

        try:

            if settings.FEATURES.get('REROUTE_ACTIVATION_EMAIL'):

                dest_addr = settings.FEATURES['REROUTE_ACTIVATION_EMAIL']

                message = ("Activation for %s (%s): %s\n" % (user, user.email, profile.name) +

                           '-' * 80 + '\n\n' + message)

                send_mail(subject, message, from_address, [dest_addr], fail_silently=False)

            else:

                user.email_user(subject, message, from_address)

        except Exception:  # pylint: disable=broad-except

            log.warning('Unable to send activation email to user', exc_info=True)

            js['value'] = _('Could not send activation e-mail.')

            # What is the correct status code to use here? I think it's 500, because

            # the problem is on the server's end -- but also, the account was created.

            # Seems like the core part of the request was successful.

            return JsonResponse(js, status=500)



    # Immediately after a user creates an account, we log them in. They are only

    # logged in until they close the browser. They can't log in again until they click

    # the activation link from the email.

    login_user = authenticate(username=post_vars['username'], password=post_vars['password'])

    login(request, login_user)

    request.session.set_expiry(0)



    # TODO: there is no error checking here to see that the user actually logged in successfully,

    # and is not yet an active user.

    if login_user is not None:

        AUDIT_LOG.info(u"Login success on new account creation - {0}".format(login_user.username))



    if DoExternalAuth:

        eamap.user = login_user

        eamap.dtsignup = datetime.datetime.now(UTC)

        eamap.save()

        AUDIT_LOG.info("User registered with external_auth %s", post_vars['username'])

        AUDIT_LOG.info('Updated ExternalAuthMap for %s to be %s', post_vars['username'], eamap)



        if settings.FEATURES.get('BYPASS_ACTIVATION_EMAIL_FOR_EXTAUTH'):

            log.info('bypassing activation email')

            login_user.is_active = True

            login_user.save()

            AUDIT_LOG.info(u"Login activated on extauth account - {0} ({1})".format(login_user.username, login_user.email))



    response = JsonResponse({

        'success': True,

        'redirect_url': try_change_enrollment(request),

    })



    # set the login cookie for the edx marketing site

    # we want this cookie to be accessed via javascript

    # so httponly is set to None



    if request.session.get_expire_at_browser_close():

        max_age = None

        expires = None

    else:

        max_age = request.session.get_expiry_age()

        expires_time = time.time() + max_age

        expires = cookie_date(expires_time)



    response.set_cookie(settings.EDXMKTG_COOKIE_NAME,

                        'true', max_age=max_age,

                        expires=expires, domain=settings.SESSION_COOKIE_DOMAIN,

                        path='/',

                        secure=None,

                        httponly=None)

    return response

```

