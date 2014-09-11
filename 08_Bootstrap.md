##Bootstrap<br>
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
Go to http://getbootstrap.com and check it out<br>
For Ruby you will be using Bootstrap Sass<br>
Add the following to your Gemfile:<br>
```
gem 'bootstrap-sass'
gem 'sass-rails'    (check to make sure you have this)
```
Next in /assets/stylesheets/application.css.scss (add scss if it does not already have it) add:<br>
```
@import "bootstrap-sprockets";
@import "bootstrap";
```
now in /assets/javascript/application.js add: <br>
```
//= require jquery  (check to make sure you have this)
//= require bootstrap-sprockets
```
Now in the terminal run bundle and restart the server:<br>
```
bundle
control c  (the button control)
rails s
```
