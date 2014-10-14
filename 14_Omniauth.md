##Stripe (Payment Processing)<br>
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
First you need to install devise and create a **devisemodel**<br>
Use the earlier walkthrough to do this<br>
We are going to set up omniauth for facebook as it is the most popular<br>
First add the omniauth facebook gem into the Gemfile:<br>
```
gem 'omniauth-facebook'
```
Then do a bundle install from the terminal:<br>
```
bundle
```
Then we need to do a migration:<br>
```
bin/rails g migration AddColumnsTo**devisemodel** provider uid
bin/rake db:migrate
```
We need to create a Facebook app id<br>
Go to https://developers.facebook.com<br>
Sign in and then click on apps then new<br>
Fill out the form and click create app<br>
Go to settings, then add platform, then website<br>
For the time being put site URL as:<br>
```
localhost:3000
```
Next in config/initializers/devise.rb add with the app id and secret keys:<br>
```
require "omniauth-facebook"
config.omniauth :facebook, "APP_ID", "APP_SECRET"    ****FIX THIS LATER SECRETS.YML**** 
```
Next go to models/**devisemodel** add this into the list for devise:<br>
```
:omniauthable, :omniauth_providers => [:facebook]
```
Now we need to adjust the routes where it says devise_for edit the line to this:<br>
```
devise_for :**devisemodels**, :controllers => { :omniauth_callbacks => "**devisemodels**/omniauth_callbacks"}
```
Next in /controllers/**devisemodels** create omniauth_callbacks_controller.rb with this:<br>
```
class **Devisemodels**::OmniauthCallbacksController < Devise::OmniauthCallbacksController
  def facebook
    # You need to implement the method below in your model (e.g. app/models/**devisemodel**.rb)
    @**devisemodel** = User.from_omniauth(request.env["omniauth.auth"])

    if @**devisemodel**.persisted?
      sign_in_and_redirect @**devisemodel**, :event => :authentication #this will throw if @**devisemodel** is not activated
      set_flash_message(:notice, :success, :kind => "Facebook") if is_navigational_format?
    else
      session["devise.facebook_data"] = request.env["omniauth.auth"]
      redirect_to new_**devisemodel**_registration_url
    end
  end
end
```
Now in /models/**devisemodel**.rb add:
```
def self.from_omniauth(auth)
  where(provider: auth.provider, uid: auth.uid).first_or_create do |**devisemodel**|
    **devisemodel**.email = auth.info.email
    **devisemodel**.password = Devise.friendly_token[0,20]
    **devisemodel**.name = auth.info.name   # assuming the **devisemodel** model has a name (remove this if you do not have a name field)
    **devisemodel**.image = auth.info.image # assuming the **devisemodel** model has an image (remove this if you do not have an image field)
  end
end
```
Now in the terminal start the server:<br>
```
rails s
```
As long as it is all working commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```