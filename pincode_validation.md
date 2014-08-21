Pincode Validation
------------------
* sudo gedit /edx/app/edxapp/edx-platform/common/djangoapps/student/models.py

```
    .........
    .........
    state = models.ForeignKey(Mooc_state)
    city = models.ForeignKey(Mooc_city)
    pincode = models.IntegerField( blank=True, db_index=True)
    .........
    .........
    
```
* sudo gedit /edx/app/edxapp/edx-platform/common/djangoapps/student/views.py

```
from django.core.validators import validate_email, validate_slug, ValidationError ,validate_integer

def _do_create_account(post_vars):
  ..............
  ..............
    moocuser = Mooc_person(user = user)
    moocuser.city_id= post_vars.get('city')
    moocuser.state_id= post_vars.get('state')
    moocuser.pincode= post_vars.get('pincode')
    
  ..............
  ..............
    try:
        profile.save()
        
    except Exception:
        log.exception("UserProfile creation failed for user {id}.".format(id=user.id))
        raise
    moocuser.save()
    UserPreference.set_preference(user, LANGUAGE_KEY, get_language())

    return (user, profile, registration,moocuser)
    .............
    .............
    
def create_account(request, post_override=None): 
 .............
 .............
 for field_name in required_post_vars:
      if field_name in ('gender', 'level_of_education','state','city','pincode'):
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
				'state': _('A state is required'),
                'country': _('A country is required'),
				'pincode': _('Pincode is required'),
		            }
           js['value'] = error_str[field_name]
           js['field'] = field_name
           return JsonResponse(js, status=400) 
      
     max_length = 6
	if field_name in ('pincode') and len(post_vars[field_name]) != max_length:
		error_str = {'pincode': _('pincode should be of length 6 and of integer type'),}
        	js['value'] = error_str['pincode']
        	js['field'] = 'pincode'
          	return JsonResponse(js, status=400)
  ...............
  ...............
     try:
        	validate_integer(post_vars['pincode'])
     except ValidationError:
        	js['value'] = _("Enter a valid integer.").format(field=a)
        	js['field'] = 'integer'
        	return JsonResponse(js, status=400)
 ...............
 ...............

```




