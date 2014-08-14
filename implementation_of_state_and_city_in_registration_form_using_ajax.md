Adding state and city field in registration form using ajax
============================================================

* Add following code to the models.py
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
