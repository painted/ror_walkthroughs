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
Then add the following information to the /models/**modelname**.rb:<br>
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
Now we are ready to add the image field to our form.<br>
So in /views/**controllername**/new.html.erb add the following field to your form:<br>
```
edit: <%= form_for @**modelname** do |f| %>
to read: <%= form_for @**modelname**, html: { multipart: true } do |f| %>
and add:	<%= f.label :avatar %>
			<%= f.file_field :avatar %> 
```
Run rspec<br>
Test should now fail due to lack of css and not lack of field<br>
Now we need to give permission for the file to be uploaded<br> 
In /controllers/**controllername**_controller.rb edit the create method:
```
edit : @**modelname** = **Modelname**.new(params[:**modelname**].permit(:**field**, :**field2**))
to read: @**modelname** = **Modelname**.new(params[:**modelname**].permit(:**field**, :**field2**, :avatar))
```
Now we will need to display the image on the page<br>
Therefore in the /views/**controllername**/index.html.erb file add:<br>
```
<%= image_tag **modelname**.avatar.url, class: 'uploaded-pic' %>
```
Run rspec<br>
All tests should pass but the image is not styled nor resized.<br>
To fix this go back to the model at /model/**modelname**.rb: <br>
```
edit: has_attached_file :avatar
to read: has_attached_file :avatar, styles: { thumb: '300X300>' }
```
and then reference this style in /views/**controllername**/index.html.erb:<br>
```
edit: <%= image_tag **modelname**.avatar.url, class: 'uploaded-pic' %>
to read: <%= image_tag **modelname**.avatar.url(:thumb), class: 'uploaded-pic' %>
```
Obviously you can create as many styles as you like in different sizes and use them as you wish.<br>
Now to make sure we do not have problems with broken images when none was chosen<br>
We can add a negative to the test when which does not upload an image<br>
Therefore in /spec/**modelname**_feature_spec.rb add a test:<br>
```
it 'does not add an image to our **controllername** if no image was added to the form' do
	visit '/**controllername**/new'
	fill_in '**Field**', with: 'Example'
	fill_in '**Field2**', with: 'Example2'
	click_button '**Buttonname**'

	expect(current_path).to eq **controllername**_path
	expect(page).to have_content 'Example'
	expect(page).not_to have_css 'img.uploaded-pic'
end
```
Run rspec<br>
The new test should fail as it will have css<br>
To fix this go to /views/**controllername**/index.html.erb:<br>
```
add:	<% if **modelname**.avatar.present? %>
before:		<%= image_tag **modelname**.avatar.url(:thumb), class: 'uploaded-pic' %>
then: 	<% end %>		afterwards
```
Run rspec<br>
All tests should pass<br>
Commit to github