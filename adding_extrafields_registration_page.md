 Adding 'country' and 'city' field in edx registration form using existing edx provided field
=============================================================================================

sudo gedit /edx/app/edxapp/edx-platform/lms/envs/common.py

Edit and replace 'hidden' to 'required' as given below



	REGISTRATION_EXTRA_FIELDS = { 
    		'level_of_education': 'optional', 
    		'gender': 'optional', 
    		'year_of_birth': 'optional', 
    		'mailing_address': 'optional', 
    		'goals': 'optional', 
    		'honor_code': 'required', 
    		'city': 'required', 
    		'country': 'required',
  
		}

Adding more non existing field in registration form like 'state' and 'pincode'
==============================================================================

#.  sudo gedit  /edx/app/edxapp/edx-platform/lms/templates/register.html
  
	<div class="group group-form group-form-secondary group-form-personalinformation"> 
        	<h2 class="sr">${_("Extra Personal Information")}</h2> 
	
        	<ol class="list-input"> 
          	% if settings.REGISTRATION_EXTRA_FIELDS['city'] != 'hidden': 
          		<li class="field ${settings.REGISTRATION_EXTRA_FIELDS['city']} text" id="field-city"> 
            			<label for="city">${_('City')}</label> 
            			<input id="city" type="text" name="city" value="" placeholder="${_('example: Bhubaneswar')}" aria-describedby="city-tip" ${'required aria-required="true"' if settings.REGISTRATION_EXTRA_FIELDS['city'] == 'required' else ''} /> 
          		</li> 
          	% endif 
	
        	% if settings.REGISTRATION_EXTRA_FIELDS['pincode'] != 'hidden': 
          		<li class="field ${settings.REGISTRATION_EXTRA_FIELDS['pincode']} text" id="field-pincode"> 
            			<label for="pincode">${_('Pincode')}</label> 
          			<input id="pincode" type="text" name="pincode" value="" placeholder="${_('example: New York')}" aria-describedby="pincode-tip" ${'required aria-required="true"' if settings.REGISTRATION_EXTRA_FIELDS['pincode'] == 'required' else ''} /> 
        		</li> 
        	% endif 
	
          	% if settings.REGISTRATION_EXTRA_FIELDS['state'] != 'hidden': 
          		<li class="field-group"> 
          		<div class="field ${settings.REGISTRATION_EXTRA_FIELDS['state']} select" id="field-state"> 
              		<label for="state">${_("State")}</label> 
              		<select id="state" name="state" ${'required aria-required="true"' ifsettings.REGISTRATION_EXTRA_FIELDS['state'] == 'required' else ''}> 
                		<option value="">--</option> 
                		%for code, state in UserProfile.STATE_CHOICES: 
                		<option value="${code}">${_(state)}</option> 
                		%endfor 
              		</select> 
            		</div> 
          		</li> 
          	% endif
		................................
		.....................................

2)  sudo gedit  /edx/app/edxapp/edx-platform/common/djangoapps/student/views.py

a)
def create_account(request, post_override=None):
  .....................................
 ...................................
#for valdating state 
  for field_name in required_post_vars: 
        if field_name in ('gender', 'level_of_education', 'state'): 
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
	    'pincode': _('A pincode is required'), 
                'city': _('A city is required'), 
	    'state': _('A state is required'), 
                'country': _('A country is required')

b) def _do_create_account(post_vars):
.........................................
.........................................
 registration.register(user) 
 
    profile = UserProfile(user=user) 
    profile.name = post_vars['name'] 
    profile.level_of_education = post_vars.get('level_of_education') 
    profile.gender = post_vars.get('gender') 
    profile.mailing_address = post_vars.get('mailing_address') 
    profile.city = post_vars.get('city') 
    profile.pincode = post_vars.get('pincode')  # for adding field in database
    profile.state = post_vars.get('state') 
    profile.country = post_vars.get('country') 
    profile.goals = post_vars.get('goals') 
...............................................
.......................................

3)    sudo gedit /edx/app/edxapp/edx-platform/lms/envs/common.py

edit and replace 'hidden' from 'required' for country as given below



REGISTRATION_EXTRA_FIELDS = { 
    'level_of_education': 'optional', 
    'gender': 'optional', 
    'year_of_birth': 'optional', 
    'mailing_address': 'optional', 
    'goals': 'optional', 
    'honor_code': 'required', 
    'city': 'required', 
    'pincode': 'required', 
    'country': 'required', 
    'state': 'required', 
}

4)sudo gedit /edx/app/edxapp/edx-platform/common/djangoapp/student/models.py

STATE_CHOICES = ( 
        ('O', ugettext_noop('ORISSA')), 
        ('U', ugettext_noop("UTTAR PRADESH")), 
        ('M', ugettext_noop("MAHARASHTRA")), 
        ('D', ugettext_noop("DELHI")) 
    ) 
city = models,TextField(blank=True, null=True)
state = models.CharField( 
        		blank=True, null=True, max_length=6, db_index=True, 
        		choices=STATE_CHOICES )
pincode = models.TextField(blank=True, null=True)


 6)  Create state and pincode field in
 Database:  edxapp
       Table : auth_userprofile
 
