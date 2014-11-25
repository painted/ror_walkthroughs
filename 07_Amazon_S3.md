##Amazon S3<br>
Created by [Will Allen, Painted Ltd]
<br>
```
**controllername** = the name of the relevent controller
**pagename** = page where you want to view results
**field** = Whatever field you want to use
**type** = Examples are text, string, decimal etc
**buttonname** = Whatever name you give the button
**devisemodel** = Replace with the user class name (e.g. User, Admin, etc.)
**modelname** = Usually the name of the controller but singular
**function** = The action that you want done.
**field_type** = Use relevent field type. Options are: check_box, color_field, date_field, datetime_field, datetime_local_field, email_field, file_field, hidden_field, month_field, number_field, password_field, phone_field, range_field, radio_button, search_field, telphone_field, text_area, text_field, time_field, url_field, week_field.
**imagename** = The name of the image file including extension.
**bucketname** = The name you gave to your S3 bucket
```
Go to http://aws.amazon.com <br>
Create an account if you do not already have one <br>
You will need to add a credit card to your account<br>
I would suggest that you set an alert for lets say $1 to know when and if you are being charged.<br>
To set this alert click on your name in the top right corner, then click Billing & Cost Management<br>
In alerts you will find: Your account is enabled for monitoring estimated charges. Set your first billing alarm to receive an e-mail when charges reach a threshold you define.  Click on "Set your first billing alarm"<br> 
Follow the instructions.<br>
Then click on your name again, Account Management and then S3<br>
Now you are going to have to create a bucket. The name must be unique.  Choose US Standard as region<br>
Next click your name and Security Credentials<br>
We are now going to have to create access keys<br>
Click on Access Keys > Create New Access Key<br>
Copy the keys into your secrets.yml (make sure that this is in your .gitignore) file as follows to development and test:<br>
```
  s3_access_key: (Put the access key id here)
  s3_secret_access_key: (Put the secret access key here)
```
Please remember that these keys are attached to your credit card so do not let anyone know them.<br>
Now add the following gems to the Gemfile:<br>
```
gem 'paperclip'
gem 'aws-sdk'
```
Run a bundle install in the terminal:<br>
```
bundle
```
Now in /models/**modelname**.rb add the follwoing information:<br>
```
Edit the line: has_attached_file :avatar, styles: { thumb: '300x300>' }
To read: 	has_attached_file :avatar, 
			styles: { thumb: '300x300>' },
			storage: :s3, 
			s3_credentials: {
			bucket: '**bucketname**',
			access_key_id: Rails.application.secrets.s3_access_key,
			secret_access_key: Rails.application.secrets.s3_secret_access_key
		}
```
Now it is important to restart the server in the terminal:<br>
```
control c
bin/rails s
```
It is probably best at this point to enter the rails console and erase all test submissions<br>
To do this go to the terminal and enter this:<br>
```
rails c
**Modelname**.delete_all
exit
```
Now go to localhost:3000/**controllername**<br>
There should be no submissions.  Go to the form and create a new submission.  It should add the submission properly and the image should now be hosted on S3<br>
To check control click on the image and click Inspect Element.<br>
You will see that the image url is now http://s3.amazonaws......<br>
Now run rspec to make sure everything works<br>
Everything should pass but they will take a long time.<br>
This is because it is writing to s3 everytime you run rspec<br>
It is probably best to therefore stub out s3 now that you know it is working<br>
To do this add to /spec/rails_helper.rb:<br>
```
require 'aws'
AWS.stub!
AWS.config(:access_key_id => "TESTKEY", :secret_access_key => "TESTSECRET")
```
Run rspec<br>
You will see that the tests run much faster<br>
Commit to Github




[Will Allen, Painted Ltd]:https://github.com/painted