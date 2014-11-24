##Emails<br>
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
**appname** = The name you want to give to your app
**secretkey** = The secret key that you generated 
```
Check to make sure all tests are passing by running rspec<br>
As we always want to start with testing let's add the capybara eamil gem to our Gemfile within test and development environments:<br>
```
gem 'capybara-email'
```
Run a bundle install:<br>
```
bundle
```
And in our /spec/rails_helper add:<br>
```
require 'capybara/email/rspec'
```
Now in the /spec/orders_feature_spec add the following test within the describe but not within any context:<br>
```
	describe 'email confirmation' do 
		before do 
			clear_emails
			Order.create(**modelname**: **modelname**, **devisemodel**:**devisemodel**)
			open_email('b@test.com')
		end

		it 'is sent when an order is created' do
			expect(current_email).to have_content 'Order successful!'
		end

		it 'references the title in the subject' do 
			expect(current_email.subject).to eq 'Title is Test'
		end
	end
```
Run rspec<br>
The test will fail as we do not have an email yet<br>
We need to now generate a mailer from the terminal:<br>
```
bin/rails g mailer OrderMailer
```
Now go to /mailers/order_mailer.rb and define a method for order confirmation:<br>
```
def confirmation(order)
	mail(to: order.**devisemodel**.email, from: 'no-reply@example.com' subject: "Title is #{order.**modelname**.title}")
end
```
Now in views you will see that a order_mailer folder has been created<br>
Create a file here called confirmation.html.erb and add the following:<br>
```
<h1>Order successful!</h1>

<p>Thank you for your order</p>

```
If you would like to do a plain text version<br>
All you have to do is create a confirmation.text file in the same folder with the text inside it<br>
Note that html is quite limited in emails (you can not use javascript, etc.)<br>
If you want to be able to see what the emails look like you should use mailcatcher<br>
To use mailcatcher add this to your Gemfile:<br>
```
gem 'mailcatcher'
```
Then run and bundle install on the terminal and start the mailcatcher server:<br>
```
bundle
mailcatcher
```
Now go to /environments/development.rb and add this (taken from http://mailcatcher.me/ ):<br>
```
config.action_mailer.delivery_method = :smtp
config.action_mailer.smtp_settings = { :address => "localhost", :port => 1025 }
```
Now whenever the tests send out an email you can see it at http://127.0.0.1:1080<br>
We can make the app send out this order confirmation email by editing the create method just before the first redirect in /controllers/charges_controller.rb:
```
new_order = Order.create(**devisemodel**: current_**devisemodel**, **modelname**: @**modelname**)
OrderMailer.confirmation(new_order).deliver
```
This way will only send out emails when an order is created through charges<br>
We probably want it so that the email goes out whenever an order is created even if not through charges<br>
So delete the changes you made in the charges controller<br>
Then we will use a callback<br>
To do this we will create a send_confirmation_email in the /models/order.rb:<br>
```
	def send_confirmation_email
		OrderMailer.confirmation(self).deliver
	end
```
In the same file we will also need to add this at the top:<br>
```
after_create :send_confirmation_email
```
Run rspec<br>
This should pass<br>
You can look at what the email looked like by going to http://127.0.0.1:1080<br>
As long as it is all working commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```
Now let's just add another line to the test to make sure the information from the order can be seen in the body of the email<br>
Add this test within the email confirmation tests:<br>
```
	it 'has information of the order in its body' do
		expect(current_email).to include 'The item ordered was Test'
	end
```
Run rspec<br>
This last test should fail<br>
To fix it we need to be able to access the order information from in our views<br>
You will need to add this line to the confirmation method in mailers/order_mails.rb:<br>
```
@order = order
```
As this is now an instance variable the view can now access the information<br>
So to make the test pass add this to the /views/order_mail/confirmation.html.erb:<br>
```
<p>The item ordered was <%= @order.**modelname**.title %></p>
```
Run rspec<br>
We should restart the rails server and test it all out<br>
Even llok at the email that was sent by going to http://127.0.0.1:1080<br>
As long as it is all working commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```
Let's now add an image to the body of the email<br>
Go back in to the confirmation view and add this:<br>
```
<%= link_to image_tag(@order.**modelname**.image.url(:medium)), **controllername**_url(host: 'localhost:3000') %>
```
Note that we are using url not path<br>
This is because path is relative<br>
We have to state the host for local testing, however, when we deploy for development the host is not necessary<br>
We are now going to deploy to heroku and use SendGrid to send the email<br>
Before we deploy lets get a couple of things ready<br>
From the terminal run:<br>
```
heroku addons:add sendgrid:starter
```
This will add the initial free SendGrid option to your heroku app which gives about 500 free emails a day<br>
Next add this to /config/environments/production.rb: (from https://devcenter.heroku.com/articles/sendgrid under Rails)<br>
```
ActionMailer::Base.smtp_settings = {
  :address        => 'smtp.sendgrid.net',
  :port           => '587',
  :authentication => :plain,
  :user_name      => ENV['SENDGRID_USERNAME'],
  :password       => ENV['SENDGRID_PASSWORD'],
  :domain         => 'heroku.com',
  :enable_starttls_auto => true
}
```
Next lets deploy it all to heroku from the terminal:<br>
```
git add .
git commit -m 'place your commit message here'
git push
bin/rake heroku:secrets RAILS_ENV=production
git push heroku master
heroku run rake db:migrate
heroku restart
heroku open
```
Now test it all out using your email and the 4242 4242 4242 4242 credit card<br>
Check to see if you get the email<br>
If so you are good to go!!