##Heroku<br>
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
**appname** = The name you want to give to your app
**secretkey** = The secret key that you generated 
```
First make sure that you have all commits and pushes up to date on Git:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```
Next from the terminal create your app on Heroku by doing this:<br>
```
heroku create **appname**
```
You must have an account with heroku and you may have to follow the instructions to login<br>
Now we need to push our app to heroku<br>
To do this go to the terminal and run this:<br>
```
git push heroku master
```
We should get an error saying the the Devise.secret_key was not set<br>
To fix this create a new secret key from the terminal by running this:<br>
```
bin/rake secret
```
Next add this to your secrets.yml file in development, test and production:
```
devise_key: **secretkey**
```
And in the /config/initializers/devise.rb do this:
```
uncomment out the line that starts with # config.secret_key 
and edit it to read this:
config.secret_key = Rails.application.secrets.devise_key
```
Now we need to get the secret keys up to heroku<br>
A quick way to fix do this is to add the following to the Gemfile:<br>
```
gem 'heroku_secrets', github: 'alexpeattie/heroku_secrets'
```
Next we need to run a bundle install and do git commit from the terminal:<br>
```
bundle
git add .
git commit -m 'place your commit message here'
git push
```
Now try to push to heroku again:<br>
```
git push heroku master
```
This should now work.<br>
As long as it does we still need to run some things in the terminal:<br>
```
bin/rake heroku:secrets RAILS_ENV=production   (this will take the keys from the secrets file and add them as environment variables)
heroku config    (this will show you the set variables)
```
If the variables have not been set<br>
Log in to your account on heroku<br>
Click on the appname then settings then reveal config variables<br>
Now manually set all of the necessary variables by clicking on edit<br>
It should look like this with variables after some of them:<br>
![Variables](https://www.paintedchef.com/variables.png)<br>
The ones you will probably need to add or edit are:
```
_SECRET_DEVISE_SECRET_KEY
_SECRET_S3_ACCESS_KEY
_SECRET_S3_SECRET_ACCESS_KEY
_SECRET_SECRET_KEY_BASE
_SECRET_KEY_BASE
```
You may still have an error with heroku<br>
The best way to know what is wrong is to add the following gem in the Gemfile:<br>
```
gem 'rails_12factor', group: :production
```
You will need to do a bundle install and will have to push to heroku:<br>
```
bundle
git add .
git commit -m 'place your commit message here'
git push heroku master
```
This will give you access to rails errors in heroku<br>
To check out the error simply do this in the terminal<br>
```
heroku logs --tail   (or simply heroku logs)
```
You will probably find that the problem is that we have not done a migrate on heroku<br>
To do the migration do this in the terminal:<br>
```
heroku run rake db:migrate
```
Now lets try opening the site again from the terminal:<br>
```
heroku open
```
It should all be working now<br>
If it did make sure to push to github:<br>
```
git push (you have already have a commit that has not been pushed)
```


