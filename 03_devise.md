###Adding Devise for Login Handling<br>
<br>
<br>
```
**controllername** = the name of the relevent controller
**pagename** = page where you want to view results
**field** = Whatever field you want to use
**type** = Examples are text, string, decimal etc
**buttonname** = Whatever name you give the button
**devisemodel** = Replace with the user class name (e.g. User, Admin, etc.)
**modelname** = Usually the name of the controller but singular
**field_type** = Use relevent field type. Options are: check_box, color_field, date_field, datetime_field, datetime_local_field, email_field, file_field, hidden_field, month_field, number_field, password_field, phone_field, range_field, radio_button, search_field, telphone_field, text_area, text_field, time_field, url_field, week_field.
```
<br>
Please note that when capitalized you should capitalize.<br>
<br>
1. Create contexts in the **controllername**_feature_spec.rb <br>
add to the tests that you want the person to have to be logged in to do:<br>
```
context 'logged in' do
end
```
2. Then comment the tests that are in the logged in context out for now.<br>
3. Add: <br>
```
context 'logged out' do
	it 'should forward user to sign in page' do
		visit '/**controllername**'
		click_link 'Create **modelname**'
		expect(page).to have_content 'Sign in'
	end
end
```
4. Run rspec<br>
5. This should not pass.  If you get an error: <br>
```
Unable to find link 'Create **modelname**'
```
Add to /views/**controllername**/index.html.erb:<br>
```
<%= link_to 'Create **modelname**', new_**modelname**_path, id: "", class: "" %>
```
id and class can be added to this link as you wish<br>
6. Run rspec<br>
7. Now you should get an error:<br>
```
expected to find text "Sign in".....
```
8. Add to gemfile:<br>
```
gem 'devise'
```
9. Run a bundle install from terminal:<br>
```
bundle
```
10. Run devise install in terminal:<br>
```
bin/rails g devise:install
```
11. Follow instructions given in terminal following devise install
12. Generate the devise model in terminal:<br>
```
bin/rails g devise **Devisemodel**
```
13. Run a migration in the terminal:<br>
```
bin/rake db:migrate
```
and if necessary:<br>
```
bin/rake db:migrate RAILS_ENV=test
```
14. Now it would be good to add devise links to pages (for all add to /views/layouts/application.html.erb) :<br>
```
<% if **devisemodel**_signed_in? %>
	<%= link_to('Logout', destroy_**devisemodel**_session_path, :method => :delete) %>
	<%= link_to('Edit Account', edit_**devisemodel**_registration_path) %>
<% else %>
	<%= link_to('Login', new_**devisemodel**_session_path) %>
	<%= link_to('Register', new**devisemodel**registration_path) %>
<% end %>
```
15. Now one can work on getting the test to pass. In /controllers/**controllername**_controller in new method add:<br>
```
authenticate_**devisename**!
```
If there are additional places that you want people to be authenticated add:<br>
```
<% if **devisename**_signed_in? %> (things that are only allowed when signed in)
<% else %> (the rest)
<% end %>
```
or in the /controllers/**controllername**_controller.rb add:<br>
```
before_action :authenticate_**devisemodel!, except: [:index] (or anything else you want anyone to see)
```
16. Uncomment out all of the tests in the features spec file.<br>
17. Run rspec
18. Add to /spec/rails_helper.rb:<br>
```
include Warden::Test::Helpers
Warden.test_mode!
```
19. Then add to **controllername**_feature_spec.rb in context logged in:<BR>
```
**devisemodel** = **Devisemodel**.create email: 's@s.com', password: '12345678', password_confirmation: '12345678'
login_as **devisemodel**
```





