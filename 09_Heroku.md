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
