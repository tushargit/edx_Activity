Adding Extra-fields(City ,State and Pincode) in CMS  Registration form
=======================================================================
### First make changes in LMS registration form then edit CMS registration form because some settings are common with lms ### 

* **sudo gedit /edx/app/edxapp/edx-platform/cms/templates/register.html**

```
   .............................................
   ...............................................
   
<!--############################   Added state dropdown         ############################### -->
        
            <li class="field text required" id="field-state">
                <label for="state">${_("state")}</label>
                <select id="state" name="state">
                    <option value="">--</option>
                 	%for state in states:                
                           <option value="${unicode(state.id)}">${ unicode(state.name) }
                    </option>
                        %endfor
              </select>
            </li>
<!-- ########################### City Added    -->
  
          <li class="field text required" id="field-city">
        
              <label for="city">${_("city")}</label>
              <select id="city" name="city" >
                <option value="">--</option>
                %for city in cities:                
                <option value="${unicode(city.id)}">${ unicode(city.name) }</option>
                %endfor   
              </select>
            
          </li>
<!-- ########################### City Pincode    -->
          <li class="field text required" id="field-pincode" >
    
              <label for="pincode">${_("Pincode")}</label>
               <input id="pincode" type="text" name="pincode" value="" placeholder="${_('example: 400076')}" required aria-required="true" aria-describedby="pincode-tip"/>
            <span class="tip tip-input" id="pincode-tip">${_('Your area pincode required here')}</span>
            
          </li>
          
          
....................................
...................................
....................................
....................................


<%block name="jsextra">
  <script type="text/javascript">
  ............................
  ............................

// Ajax for state and city ///////////
require(["jquery", "jquery.cookie"], function($) {


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
    
     </script>
</%block>
```
* **sudo gedit /edx/app/edxapp/edx-platform/cms/djangoapps/contentstore/views/public.py**

```
from student.models import (
    Mooc_state,Mooc_city,Mooc_person #######  three tables are added
)
..............................
...............................

@ensure_csrf_cookie
def signup(request):
    """
    Display the signup form.
    """
    states=Mooc_state.objects.all() 
    city = Mooc_city.objects.all() 
    csrf_token = csrf(request)['csrf_token']
    if request.user.is_authenticated():
        return redirect('/course')
    if settings.FEATURES.get('AUTH_USE_CERTIFICATES_IMMEDIATE_SIGNUP'):
        # Redirect to course to login to process their certificate if SSL is enabled
        # and registration is disabled.
        return redirect(reverse('login'))
    
    return render_to_response('register.html', {'csrf': csrf_token ,'states':states,'cities':city})

...............................
...............................
```

* **sudo gedit /edx/app/edxapp/edx-platform/cms/djangoapps/contentstore/views/public.py**
* **sudo gedit /edx/app/edxapp/edx-platform/cms/urls.py**

```
.....................
.....................

# User creation and updating views
urlpatterns += patterns(
    '',

    url(r'^create_account$', 'student.views.create_account', name='create_account'),
    url(r'^activate/(?P<key>[^/]*)$', 'student.views.activate_account', name='activate'),
    url(r'^city_ajax$', 'student.views.city_ajax', name="city_ajax"),

    # ajax view that actually does the work
    url(r'^login_post$', 'student.views.login_user', name='login_post'),
    url(r'^logout$', 'student.views.logout_user', name='logout'),
    url(r'^embargo$', 'student.views.embargo', name="embargo"),
)
....................................
.....................................

```
 



