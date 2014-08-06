Reflecting changes of models.py in sql database(Creating table in mysql using models.py)
========================================================================================


* sudo -u www-data /edx/bin/python.edxapp ./manage.py lms migrate --delete-ghost-migrations --settings aws

* sudo -u www-data /edx/bin/python.edxapp ./manage.py lms schemamigration student --initial --settings aws 

* sudo -u www-data /edx/bin/python.edxapp ./manage.py lms migrate student 0001_initial --fake --settings aws 
* sudo gedit /edx/app/edxapp/edx-platform/common/djangoapps/student/models.py 

 paste code given below at end of page


```
#mycode 
class MoocPincode(models.Model): 
    """This is our representation of the User for mooc courses 

""" 
    pincode = models.IntegerField(unique=True, max_length=6, blank=True, db_index=True) 
    
    @property 
    def email(self): 
        return self.pincode 



#mycode 

```





* sudo -u www-data /edx/bin/python.edxapp ./manage.py lms schemamigration student --initial --settings aws 

 output of above command
```
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

```

* sudo -u www-data /edx/bin/python.edxapp ./manage.py lms migrate student 0002_initial --fake --settings aws 


* sudo -u www-data /edx/bin/python.edxapp ./manage.py lms syncdb  --all  --settings aws
```
output of above command given below
2014-08-06 09:08:33,280 INFO 15971 [dd.dogapi] dog_stats_api.py:66 - Initializing dog api to use statsd: localhost, 8125
Syncing...
Creating tables ...
Creating table student_moocpincode

...................................................
.......................................................


```

 *sudo -u www-data /edx/bin/python.edxapp ./manage.py lms migrate --fake     --settings aws 

 *sudo -u www-data /edx/bin/python.edxapp ./manage.py lms syncdb --migrate      --settings aws 
