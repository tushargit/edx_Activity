For implementing change  language option on home page (or before login/authentication)   
=======================================================================================
*  **sudo  gedit /edx/app/edxapp/edx-platform/common/djangoapps/student/views.py**

*  **After  changes your  index function will look like given below:**
```ruby
   index(request, extra_context={}, user=AnonymousUser()): 
    '''
    Render the edX main page. 

    extra_context is used to allow immediate display of certain modal windows, eg signup, 
    as used by external_auth. 
    '''
    
    language_options = DarkLangConfig.current().released_languages_list 
   
   '''
     add in the default language if it's not in the list of released languages 
   '''
   
    if settings.LANGUAGE_CODE not in language_options: 
        language_options.append(settings.LANGUAGE_CODE) 
        # Re-alphabetize language options 
        language_options.sort() 
   
   '''
    try to get the prefered language for the user 
   
   ''' 
    cur_lang_code = UserPreference.get_preference(request.POST.get('user'), LANGUAGE_KEY) 
    if cur_lang_code: 
       '''
         if the user has a preference, get the name from the code 
        '''
        current_language = settings.LANGUAGE_DICT[cur_lang_code] 
    else: 
        '''
         if the user doesn't have a preference, use the default language 
        '''
        current_language = settings.LANGUAGE_DICT[settings.LANGUAGE_CODE] 
   



    '''
    The course selection work is done in courseware.courses. 
    '''
    domain = settings.FEATURES.get('FORCE_UNIVERSITY_DOMAIN')  # normally False 
    '''
    do explicit check, because domain=None is valid 
    '''  
    if domain is False: 
        domain = request.META.get('HTTP_HOST') 

    courses = get_courses(user, domain=domain) 
    courses = sort_by_announcement(courses) 

    context = {'courses': courses, 
                'language_options': language_options, 
                'current_language': current_language, 
                'current_language_code': cur_lang_code,} 

    context.update(extra_context) 
    return render_to_response('index.html', context) 
   ``` 
   
* **sudo gedit /edx/app/edxapp/edx-platform/lms/templates/index.html**

* **Make following  changes in html code of index.html**

```ruby
<%include file='modal/_modal-settings-language.html' /> 

<header class="global" aria-label="${_('Global Navigation')}"> 
        <nav><ol class="right nav-courseware"> 
    <li class="nav-courseware-02"> 
	    %if len(language_options) > 1: 
	   
	    <%include file='dashboard/_dashboard_info_language.html' /> 
	    %endif 
            </li> 
        </ol></nav> 
</header>
 

 <script type="text/javascript"> 
  $(window).load(function() {$('#signup_action').trigger("click");}); 


(function() { 

    $('.message.is-expandable .wrapper-tip').bind('click', toggleExpandMessage); 
     $("#submit-lang").click(function(event, xhr) { 
        event.preventDefault(); 
        $.post('/lang_pref/setlang/', 
            {"language": $('#settings-language-value').val()}) 
        .done( 
            function(data){ 
                // submit form as normal 
                $('.settings-language-form').submit(); 
            } 
        ); 
    }); 
  })(this); 
 

</script>

 ```
