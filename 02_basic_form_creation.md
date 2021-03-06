###Creating a form once the initial set up has been completed.
<br>
Created by [Will Allen, Painted Ltd]
<br>
<br>
```
**controllername** = the name of the relevent controller
**pagename** = page where you want to view results
**field** = Whatever field you want to use
**type** = Examples are text, string, decimal etc
**buttonname** = Whatever name you give the button
**modelname** = Usually the name of the controller but singular
**field_type** = Use relevent field type. Options are: check_box, color_field, date_field, datetime_field, datetime_local_field, email_field, file_field, hidden_field, month_field, number_field, password_field, phone_field, range_field, radio_button, search_field, telphone_field, text_area, text_field, time_field, url_field, week_field.
```
<br>
<br>
Please note that when capitalized you should capitalize.<br>
<br>
Create a test in **controllername**_feature_spec.rb:<br>
```
describe 'creating **controllername**' do
	it 'adds the **modelname** to the **pagename**'
		visit '/**controllername**/new'
		fill_in '**field**', with: 'Example Text' (repeat for multiple fields)
		click_button '**buttonname**'

		expect(current_path).to eq **controllername**_path
		expect(page).to have_content 'Example Text'
	end
end
```
rspec<br>
in **controllername**_controller.rb define a method for new:<br>
```
def new
	@**modelname** = **Modelname**.new
end
```
rspec<br>
Create new.html.erb in app/views/**controllername**/<br>
rspec<br>
In views/**controllername**/new.html.erb add:<br>
```
<%= form_for @**modelname** do |f| %>
	<%= f.label :**field** %>         (repeat this and next line for each field)
	<%= f.**field_type** :**field** %>  

	<%= f.submit '**buttonname**' %>
<% end %>
```
rspec<br>
In **controllername**_controller.rb create create method:<br>
```
def create

end
```
rspec<br>
In create method add:<br>
```
@**modelname** = **Modelname**.new(params[:**modelname**].permit(:**field**))  (add all text fields separated by a comma)
@**modelname**.save!
redirect_to **controllername**_path
```
rspec (should be all clear)<br>
rails s (to see it in your browser)<br>
In browser go to localhost/3000/**controllername** to see a list of the items<br>
In browser go to localhost/3000/**controllername**/new to see the form
<br>
<br>
Adding additional fields not already in database<br>
Any fields that are being created must be in the database.  If you want to add an additional field not included when making the model do a db migration using: <br>
```
bin/rails g migration Add**Field**To**Controllername** **field**:**type**
bin/rake db:migrate
```
You can add more than one field at a time just by adding more **field**:**type** with a space between each one.<br>
Commit to Github


[Will Allen, Painted Ltd]:https://github.com/painted