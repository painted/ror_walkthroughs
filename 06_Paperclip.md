###In Progress

##Paperclip<br>
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
```
<br>
First create an images folder in the spec directory and place a test image in the folder<br>
Then in /spec/**modelname**_feature_spec.rb add a test:<br>
```
it 'can add a photo to our **controllername**' do
	visit '/**controllername**/new'
	fill_in '**Field**', with: 'Example'
	fill_in '**Field2**', with: 'Example2'
	attach_file '**Field3**, Rails.root.join('spec/images/**imagename**')
	click_button '**Buttonname**'

	expect(current_path).to eq **controllername**_path
	expect(page).to have_content 'Example'
	expect(page).to have_css 'img.uploaded-pic'
end
```
Run rspec<br>
The test will fail as there is no file field 'Image'<br>
If you don't already have imagemagick you will now have to install it from the terminal:<br>
```
brew install imagemagick
```
As long as imagemagick is installed add to the follwing to Gemfile:<br>
```
gem 'paperclip', '~> 4.2'
```
Then do a bundle install in the terminal:<br>
```
bundle
```
Then add the following information to the **modelname**:<br>
```
	has_attached_file :avatar
 	validates_attachment_content_type :avatar, :content_type => /\Aimage\/.*\Z/
```
Please note that avatar can be changed to whatever word you like.<br>
Next in the terminal create a migration for paperclip:<br>
```
bin/rails g paperclip **modelname** avatar

bin/rake db:migrate
```
Now we are ready to add the image field to our form. 

