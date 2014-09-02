###User Accountability<br>
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
**function** = The action that you want done.
**field_type** = Use relevent field type. Options are: check_box, color_field, date_field, datetime_field, datetime_local_field, email_field, file_field, hidden_field, month_field, number_field, password_field, phone_field, range_field, radio_button, search_field, telphone_field, text_area, text_field, time_field, url_field, week_field.
```
<br>
Create a **function**_**controllername**_feature_spec.rb file in /spec/features.<br>
Add the following tests:<br>
```
require 'rails_helper'

context 'logged out' do 
	before { **Modelname**.create(**field**: 'Example', **field2**: 'Example2')}
	it 'cannot **function** **controllername**' do
		visit '/**controllername**'
		expect(page).to have_content 'Example (Example2)'
		expect(page).not_to have_link '**Function** Example'
	end
end

context 'logged in as the **modelname** creator' do 
	before do
		bob = **Devisemodel**.create(email: 'bob@example.com', password: '12345678', password_confirmation: '12345678')
		login_as bob
		bob.**controllername**.create(**field**: 'Example', **field2**: 'Example2')
	end
	it 'can **function** **controllername**' do
		visit '/**controllername**'
		click_link '**Function** Example'
		expect(page)._to have_content 'Successfully **Function** Example'
	end
end

context 'logged in as another **modelname** creator' do 
	before do
		will = **Devisemodel**.create(email: 'will@example.com', password: '12345678', password_confirmation: '12345678')
		bob = **Devisemodel**.create(email: 'bob@example.com', password: '12345678', password_confirmation: '12345678')
		login_as will
		bob.**controllername**.create(**field**: 'Example', **field2**: 'Example2')
	end
	it 'cannot **function** **controllername**' do
		visit '/**controllername**'
		click_link '**Function** Example'
		expect(page)._to have_content 'You do not have permission to **Function** Example'
	end
end
```
Run rspec<br>
First failure should be unknown attribute **devisemodel**<br>
To fix this error run this in the terminal:<br>
```
bin/rails g migration Add**Devisename**IdTo**Controllername** **devisename**:belongs_to
bin/rake db:migrate
```
Next add some code to the /models/**devisename**.rb:<br>
```
has_many :**controllername**
```
And in /models/**modelname**.rb:<br>
```
belongs_to :**devisemodel**
```
Run rspec<br>
Most tests should now pass.<br>
To make all pass we need to save the current **devisename** in the create function.<br>
Therefore in /controllers/**controllername**_controller.rb add to the create method:<br>
```
@**modelname**.**devisename** = current_**devisename** (just before the save)
```
And in the same file in the **function** method add:<br>
```
@**modelname** = current_**devisemodel**.**controllername**.find params[:id]

rescue ActiveRecord::RecordNotFound     (Add this at the bottom of the method)
	flash[:notice] = 'You do not have permission to **Function** Example'
ensure
	redirect_to '/**controllername**'
```
Run rspec<br>
Now everything should pass.
