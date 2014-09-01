###In Progress

Adding Devise for Login<br>
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
Please note that when capitalized you should capitalize.<br>
<br>
1. Create contexts in the '**controllername**'_feature_spec.rb <br>
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
		click_link 'Create '
		expect(page).to have_content 'Sign in'
	end
end
```


