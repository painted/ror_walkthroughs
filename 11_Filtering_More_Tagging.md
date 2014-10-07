##Filtering (More Tagging)<br>
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
We will now continue from the tagging walkthrough in order to have additional usability<br>
Let's first use the bootstrap tags input to make the tag input more user friendly<br>
Go to http://timschlechter.github.io/bootstrap-tagsinput/examples/ <br>
Click on the link at the top Download(latest)<br>
Now open up the zip file that was downloaded and you will find a folder with a number of files<br>
Find bootstrap-tagsinput.js and move it to vendor/assets/javascript/<br>
Find bootstrap-tagsinput.css and move it to vendor/assets/stylesheets/<br>
Next go to /assets/javascript/application.js and add this:<br>
```
//= require bootstrap-tagsinput
```
Then in /assets/stylesheets/application.css.scss add:<br>
```
@import "bootstrap-tagsinput";
```
Now in the form you will need to do this:<br>
```
change this: <%= f.text_field :tag_names %><br>
to read this: <%= f.text_field :tag_names, data: {role: 'tagsinput'} %><br>
```
Restart the rails server in the terminal:<br>
```
control c (the button control) to stop the server
rails s
```
Check to see that it is work by going to localhost:3000/**controllername**/new and fill out the form<br>
You may need to edit the tag_names writer method in /models/**modelname** to read this:<br>
```
	def tag_names=(tag_list)
		tag_list.split(',').each do |tag|
			tags << Tag.find_or_create_by(name: tag)
		end
	end
```
This is due to the way that bootstrap input tags creates the list<br>
As long as it is all working commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```
Next in the tags_feature_spec.rb add the following test within the logged in context:<br>
```
	it 'should filter **controllername** by selected tag' do
		bob.**controllername**.create title: 'Pic1', tag_names: 'yolo'
		bob.**controllername**.create title: 'Pic2', tag_names: 'swag'

		visit '/**controllername'
		click_link 'yolo'
		expect(page).to have_css 'h1', '**Controllername** associated with Yolo'
		expect(page).to have_content 'Pic1'
		expect(page).not_to have_content 'Pic2'
	end
```
Run rspec
You should get an error that it can not find the css<br>
We will need to create some routes to fix this by going to /config/routes.rb and adding:<br>
```
resources :tags
```
and in the views/**categoryname**/index.html.erb do this:<br>
```
edit: <%= link_to tag.name, '#' %>
to read this: <%= link_to tag.name, tag_path %>
```
Now we will need to create a controller for Tags<br>
To do this go to the terminal and run this:<br>
```
bin/rails g controller Tags
```
Now in the /controllers/tags_controller.rb we need to create a show method:<br>
```
def show
	@tag = Tag.find params[:id]
end
```
You will now need to create a tags/show template in the views<br>
Create the file show.html.erb in views/tags/ with the following information:<br>
```
<h1>**Controllername** associated with <%= @tag.name %></h1>
```
Run rspec
The test will now fail for a different reason<br>
The new reason is expected to find text "Pic1"<br>
To fix this we need to add more info to the show.html.erb template:<br>
```
<% @tag.**controllername**.each do |**modelname**| %>
	<%= **modelname**.title %>
<% end %>
```
This will make the tests pass but it is not very useful as it only gives the titles of the items<br>
We could copy and paste the format from the index.html.erb file but that would not be very dry<br>
So lets create a partial<br>
To do this in /views/**controllername** create a new file called _**modelname**.html.erb<br>
Now extract the following from /views/**controllername**/index.html.erb:<br>
```
<div class='col-md-2'>
	<div class="image-link">
		<%= image_tag **modelname**.image.url(:small)), class: 'uploaded-pic' %><br>
	</div>
	<div class='details'>
		<h4><%= **modelname**.title %></h4>
		<p><%= **modelname**.description[0..14] %></p>
		<p><% **modelname**.tags.each do |tag| %>
			<%= link_to tag.name, tag_path(tag) %>
		<% end %></p>
	</div>
</div>
```
And add it to the file you just created<br>
Then you need to edit the /views/**controllername**/index.html.erb:<br>
```
remove: <% @**controllername**.each do |**modelname**| %>
		<% end %>
and add this: <%= render @**controllername** %>
```
Then to use it in the /views/tags/show.html.erb edit the file to read this:<br>
```
<h1>**Controllername** associated with <%= @tag.name.capitalize %></h1>
<div class='container'>
	<div class='row'>
		<%= render @tag.**controllername** %>
	</div>
</div>
```
Run rspec<br>
As long as it is all working commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```
You will see that the url uses the id of the tag<br>
Lets get rid of the id and use the actual tag name<br>
To do this let's first write a test for it<br>
Lets rework the tagging feature tests to look like this:<br>
```
	context 'existing posts' do 

		before do
			bob.**controllername**.create title: 'Pic1', tag_names: 'yolo'
			bob.**controllername**.create title: 'Pic2', tag_names: 'swag'
			visit '/**controllername'
		end

		it 'should filter **controllername** by selected tag' do

			click_link 'yolo'
			expect(page).to have_css 'h1', '**Controllername** associated with Yolo'
			expect(page).to have_content 'Pic1'
			expect(page).not_to have_content 'Pic2'
		end

		it 'uses the tag name in the url' do

			click_link 'yolo'
			expect(current_path).to eq '/tags/yolo'
		end
	end
```
Run rspec<br>
You will see that the new test will fail<br>
Now go to the /models/tag.rb and add this just before the end:<br>
```
def to_param
	name
end
```
Run rspec<br>
This should work but now two other tests are failing<br>
This is because we now need to change something in the controller<br>
Go to /controller/tags_controller.rb and edit the show method to this:<br>
```
	def show
		@tag = Tag.find_by_name params[:id]
	end
```
Run rspec<br>
As long as it is all working commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```

