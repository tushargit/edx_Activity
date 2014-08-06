Reflecting changes of models.py in sql database(Creating table in mysql using models.py)
========================================================================================


 * sudo -u www-data /edx/bin/python.edxapp ./manage.py lms migrate --delete-ghost-migrations --settings aws

 * sudo -u www-data /edx/bin/python.edxapp ./manage.py lms schemamigration student --initial --settings aws 

* sudo -u www-data /edx/bin/python.edxapp ./manage.py lms migrate student 0001_initial --fake --settings aws 



'''

#mycode 
class MoocPincode(models.Model): 
    """This is our representation of the User for mooc courses 

""" 
    pincode = models.IntegerField(unique=True, max_length=6, blank=True, db_index=True) 
    
    @property 
    def email(self): 
        return self.pincode 

class MoocCity(models.Model): 
    """This is our representation of the User for mooc courses 

""" 
    city_name = models.CharField(unique=True, max_length=45, blank=True, db_index=True) 
    pincode  =  models.ForeignKey(MoocPincode) 
    @property 
    def email(self): 
        return self.cit_name 

class MoocState(models.Model): 
    """This is our representation of the User for mooc courses 

""" 
    
    state_name = models.CharField(unique=True, max_length=45, blank=True, db_index=True) 
    city =  models.ForeignKey(MoocCity) 


    @property 
    def email(self): 
        return self.state_name 


 
class MoocUser(models.Model): 
    """This is our representation of the User for mooc courses 

""" 
    # Our own record keeping... 
    #STATE_CHOICES = ( [(x['state_name'], str(x['state_name'])) for x in MoocState.objects.values('state_name')]) 
  
    user = models.OneToOneField(User, unique=True, default=None) 
  #  created_at = models.DateTimeField(auto_now_add=True, db_index=True) 
   # updated_at = models.DateTimeField(auto_now=True, db_index=True) 
    city =  models.ForeignKey(MoocCity,  default=None) 
    state =  models.ForeignKey(MoocState,  default=None ) 
    pincode = models.ForeignKey(MoocPincode, default=None) 
   
    @property 
    def email(self): 
        return self.user.username 

    def set_login_session(self, session_id=None): 
        """ 
        Sets the current session id for the logged-in user. 
        If session_id doesn't match the existing session, 
        deletes the old session object. 
        """ 
        meta = self.get_meta() 
        old_login = meta.get('session_id', None) 
        if old_login: 
            SessionStore(session_key=old_login).delete() 
        meta['session_id'] = session_id 
        self.set_meta(meta) 
        self.save() 






#mycode 





* sudo -u www-data /edx/bin/python.edxapp ./manage.py lms schemamigration student --initial --settings aws 
2014-08-06 08:46:59,056 INFO 15477 [dd.dogapi] dog_stats_api.py:66 - Initializing dog api to use statsd: localhost, 8125 
 + Added model student.AnonymousUserId 
 + Added model student.UserStanding 
 + Added model student.UserProfile 
 + Added model student.UserSignupSource 
 + Added model student.UserTestGroup 
 + Added M2M table for users on student.UserTestGroup 
 + Added model student.Registration 
 + Added model student.PendingNameChange 
 + Added model student.PendingEmailChange 
 + Added model student.PasswordHistory 
 + Added model student.LoginFailures 
 + Added model student.CourseEnrollment 
 + Added unique constraint for ['user', 'course_id'] on student.CourseEnrollment 
 + Added model student.CourseEnrollmentAllowed 
 + Added unique constraint for ['email', 'course_id'] on student.CourseEnrollmentAllowed 
 + Added model student.CourseAccessRole 
 + Added unique constraint for ['user', 'org', 'course_id', 'role'] on student.CourseAccessRole 
 + Added model student.MoocPincode 
 + Added model student.MoocCity 
 + Added model student.MoocState 
 + Added model student.MoocUser 
Created 0002_initial.py. You can now apply this migration with: ./manage.py migrate student 



* sudo -u www-data /edx/bin/python.edxapp ./manage.py lms migrate student 0002_initial --fake --settings aws 


* sudo -u www-data /edx/bin/python.edxapp ./manage.py lms syncdb  --all  --settings aws

2014-08-06 09:08:33,280 INFO 15971 [dd.dogapi] dog_stats_api.py:66 - Initializing dog api to use statsd: localhost, 8125
Syncing...
Creating tables ...
Creating table student_moocpincode
Creating table student_mooccity
Creating table student_moocstate
Creating table student_moocuser
...................................................
.......................................................


 *sudo -u www-data /edx/bin/python.edxapp ./manage.py lms migrate --fake     --settings aws 

 *sudo -u www-data /edx/bin/python.edxapp ./manage.py lms syncdb --migrate      --settings aws 
