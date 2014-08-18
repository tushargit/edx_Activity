Adding state and city field in registration form using ajax
============================================================

* **sudo gedit  /edx/app/edxapp/edx-platform/common/djangoapps/student/models.py**
```
....................
....................

class Mooc_state(models.Model):      

      name=models.CharField(max_length=255,unique=True,db_index=True,null=False,default=None)

     

      def __unicode__(self):

          return self.name





class Mooc_city(models.Model):

     

      name= models.CharField(max_length=255,null=False,default=None)

      state = models.ForeignKey(Mooc_state)

      def __unicode__(self):

          return self.name

          

class Mooc_person(models.Model):

      user = models.OneToOneField(User,null=False,default=None)

      state = models.ForeignKey(Mooc_state,default=None)

      city=models.ForeignKey(Mooc_city,default=None)

      pincode = models.IntegerField(max_length=25,default=None)

      
      def __unicode__(self):

          return self.user_id.username  
         
.......................
.......................

```

* **sudo -u www-data /edx/bin/python.edxapp ./manage.py lms schemamigration student --initial --settings aws**

* **sudo -u www-data /edx/bin/python.edxapp ./manage.py lms syncdb --all --settings aws**

* **sudo -u www-data /edx/bin/python.edxapp ./manage.py lms migrate --fake --settings aws**

* **Insert data in student_mooc_state and student_mooc_city table of edxapp database**  

### To create state , city and pincode fields in html###

* **sudo gedit  /edx/app/edxapp/edx-platform/lms/templates/register.html** 
```

<!--############################   Added state dropdown         ############################### -->
         % if settings.REGISTRATION_EXTRA_FIELDS['state'] != 'hidden':
          <li class="field-group">
         <div class="field ${settings.REGISTRATION_EXTRA_FIELDS['state']} select" id="field-state">
              <label for="state">${_("state")}</label>
              <select id="state" name="state" ${'required aria-required="true"' if settings.REGISTRATION_EXTRA_FIELDS['state'] == 'required' else ''}>
                <option value="">--</option>
                %for state in states:                
                <option value="${unicode(state.id)}">${ unicode(state.name) }</option>
                %endfor
              </select>
            </div>
          </li>
          % endif
      


<!-- ############################   Added city dropdown                 ############################### -->

      % if settings.REGISTRATION_EXTRA_FIELDS['city'] != 'hidden':
          <li class="field-group">
         <div class="field ${settings.REGISTRATION_EXTRA_FIELDS['city']} select" id="field-city">
              <label for="city">${_("city")}</label>
              <select id="city" name="city" ${'required aria-required="true"' if settings.REGISTRATION_EXTRA_FIELDS['city'] == 'required' else ''}>
                <option value="">--</option>
                %for city in cities:                
                <option value="${unicode(city.id)}">${ unicode(city.name) }</option>
                %endfor   
              </select>
            </div>
          </li>
          % endif




<!-- ############################    Changes done               ############################### -->
 % if settings.REGISTRATION_EXTRA_FIELDS['pincode'] != 'hidden':
          <li class="field-group">
            <div class="field ${settings.REGISTRATION_EXTRA_FIELDS['pincode']} select" id="field-pincode">
              <label for="pincode">${_("Pincode")}</label>
               <input id="pincode" type="text" name="pincode" value="" placeholder="${_('example: 400076')}" required aria-required="true" aria-describedby="pincode-tip"/>
            <span class="tip tip-input" id="pincode-tip">${_('Will be shown in any discussions or forums you participate in')} <strong>(${_('cannot be changed later')})</strong></span>
            </div>
          </li>
          % endif
```


### To enable state,city and picode from exrafields  ###

*  **sudo gedit /edx/app/edxapp/edx-platform/lms/envs/common.py and add state,city and pincode as following**
```
REGISTRATION_EXTRA_FIELDS = {
    'level_of_education': 'optional',
    'gender': 'optional',
    'year_of_birth': 'optional',
    'mailing_address': 'optional',
    'goals': 'optional',
    'honor_code': 'required',
    'pincode': 'required',
    'country': 'hidden',
    'state':'required',
    'city':'required',
}

```
### To validate state,city and pincode 

* **sudo gedit /edx/app/edxapp/edx-platform/common/djangoapps/student/views.py as following**

```
Change No 1:
import json
from student.models import (
    Registration, UserProfile, PendingNameChange,
    PendingEmailChange, CourseEnrollment, unique_id_for_user,
    CourseEnrollmentAllowed, UserStanding, LoginFailures,        
create_comments_service_user, PasswordHistory,Mooc_state,Mooc_city,Mooc_person 
)


 Change NO 2: change in  _do_create_account(post_vars):
   for field_name in required_post_vars: 
        if field_name in ('gender', 'level_of_education','state','city'): 
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
                'pincode':_('Pincode is required')


            }
```
### To  fetch state and city data from database and use in form ###

* **sudo gedit /edx/app/edxapp/edx-platform/common/djangoapps/student/views.py as following**
```
change NO 1: Change the register_user function 

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
### To populate city based on state selected using ajax

* **sudo gedit  /edx/app/edxapp/edx-platform/lms/templates/register.html for sending selected state id** 

```  

<%block name="js_extra">
  <script type="text/javascript">
  .......................
  .......................
  ........................
  ........................
   (function() {
    

      $('#state').on('change', function() {   // check for changes
         $('#city').empty();         //  Make the city dropdown empty
         //alert($('#state').val())
          
          // Now it's time to fill the City drop down based on the state selection


        $.ajax({
           url:"/city_ajax", 
           type: "post",
           data:{id: $('#state').val()},
           contentType: "application/json; charset=utf-8",
           datatype: 'json',
           success: function(result)
                 { 
                   //result = JSON.parse(result);
                   //alert(result)
                   //Next four lines are to add select option to the dropdown
                    //var option1 = document.createElement('option');
                    var listItems= "";
                    var teams = eval('(' + result + ')');
                    //option1.value="Select";
                    //option1.text="Select";
                    //$("#city").append(option1);
                    for(var c=0; c<teams.length; c++){ 
		        //var option = document.createElement('option');
		         //option.value= result[c].fields.state
		         //option.text=  result[c].fields.name
                       
                    //listItems+= "<option value='" + result.Table[c].id + "'>" + result.Table[c].name + "</option>";
                         listItems += "<option value='" + teams[c].pk+ "'>" + teams[c].fields.name + "</option>";
   
                   // $("#city").append(option);
                     $("#city").html(listItems);
                 }

                    
                   // this for loop get the json array and put every value one by 
                   //one in the option field and finally apeend it to the select.
                 

            },
           
    });

 


      });

   
    
   
    })(this);


.........................
.........................
 



  </script>
</%block>
```

* **sudo gedit  /edx/app/edxapp/edx-platform/common/djangoapps/student/views.py for using city dropdownlist based on state selected**   
```

changes 1: this is a new function caled by AJAX to acces states based on the selected state id.
@ensure_csrf_cookie

def city_ajax(request):

    if request.is_ajax() and request.method =='POST':

       states = request.POST.get('id')

       city = Mooc_city.objects.filter(state_id=states)

    data = serializers.serialize('json', city)

    return HttpResponse(json.dumps(data), mimetype="application/json")

```

*  **sudo  gedit /edx/app/edxapp/edx-platform/lms/urls.py for creating urls to city_ajax function**

```
.....................
.....................

    url(r'^register$', 'student.views.register_user', name="register_user"),
    url(r'^city_ajax$', 'student.views.city_ajax', name="city_ajax"), ##-- handeled the ajax request sent on the selection of State
    url(r'^admin_dashboard$', 'dashboard.views.dashboard'),

.........................
.........................
```

*  **sudo gedit  /edx/app/edxapp/edx-platform/common/djangoapps/student/views.py  for inserting state ,city and pincode in sql database**
```
change NO: 2 change the _do_create_account function
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



    .....................
    ....................
    .....................
    .....................

    registration.register(user)



    profile = UserProfile(user=user)

    mooc_person = Mooc_person(user=user) 
  
 
    mooc_person.pincode = post_vars.get('pincode')

    mooc_person.state_id=post_vars.get('state')

    mooc_person.city_id = post_vars.get('city')



.....................
.....................
......................
......................

UserPreference.set_preference(user, LANGUAGE_KEY, get_language())



    return (user, profile, registration,mooc_person)
.....................
.....................

change : 6  change the create account function
@ensure_csrf_cookie

def create_account(request, post_override=None):

    """

    JSON call to create new edX account.

    Used by form in signup_modal.html, which is included into navigation.html

    """

......................
......................


    (user, profile, registration,mooc_user) = ret



    dog_stats_api.increment("common.student.account_created")

    create_comments_service_user(user)



    context = {

        'name': post_vars['name'],

        'key': registration.activation_key,

    }

.........................
.........................


   
```

