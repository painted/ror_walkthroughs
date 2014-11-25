##Tagging (Many to Many Relationship)<br>
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
Lets now add tags to the app<br>
Tags are a good example of a many to many relationship as a post for example can have many tags and a tag can have many posts<br>
Now lets write a test<br>
Create a tags_feature_spec.rb file in /spec/features with the following information:<br>
```
require 'rails_helper'

describe 'tagging **controllername** with link' do
	context 'when logged in' do

		before do 
			bob = **Devisemodel**.create(email: 'bob@bob.com', password: '12345678', password_confirmation: '12345678')
			login_as bob
		end

		it 'adds the **modelname** to the homepage' do 
			visit '/**controllername**/new'
			fill_in '**Field**', with: 'Example'
			fill_in '**Field2**', with: 'Example2'
			fill_in 'Tags', with: 'TagExample, TagExample2'
			click_button '**Buttonname**'

			expect(page).to have_link 'TagExample'
			expect(page).to have_link 'TagExample2'
		end
	end 
end
```
Run rspec<br>
You will get an error saying that there is no field for tags<br>
Therefore go to your form and add the field as so:<br>
```
<%= f.label :tag_names, 'Tags' %>
<%= f.text_field :tag_names %>
```
Run rspec<br>
Anything to do with the form will go red<br>
Do not create a column now to the database<br>
We are now going to create reader and writer methods in out **modelname**.rb<br>
```
def tag_names
	
end

def tag_names=(tag_list)

end
```
In order for the tag information to come through you must permit the tag_names<br>
To do this go to /**controllername**_controller.rb and add :tag_names to the list after .permit<br>
Next we should set up an unit test<br>
Go to /spec/models/**modelname**_spec.rb and edit the info to read this:<br>
```
require 'rails_helper'

describe Category do
	let(:category) { Category.create }

	describe '#tag_names=' do 
		describe 'no tags' do
			it 'does nothing' do
				expect { **modelname**.tag_names = ''}.not_to raise_error
			end
		end
	end 
end
```
Run rspec<br>
The unit test should pass, however, the feature test will still fail<br>
Now add another test to the same file:<br>
```
	describe 'one tag that does not exist' do 
		it 'adds a tag to the **modelname**' do
			**modelname**.tag_names = 'yolo'
			expect(**modelname**.tags.length).to eq 1
		end
	end
```
Run rspec<br>
We need to create a link between the **modelname** and the tags.<br>
As it is many to many one must go to the /models/**modelname**.rb and add this:<br>
```
has_and_belongs_to_many :tags
```
Next we need to generate a model for the Tag in the terminal by doing this:<br>
```
bin/rails g model Tag name:string
```
Now in the /models/tag.rb file add:<br>
```
has_and_belongs_to_many :**controllername**
```
Run a migration<br>
```
bin/rake db:migrate
bin/rake db:migrate RAILS_ENV=test
```
Run rspec<br>
You will get a new error which says that a join table is missing<br>
Rails makes it easy to fix<br>
From the terminal run this to create a join table:<br>
```
bin/rails g migration CreateJoinTable**Controllername**Tags **modelname** tag
bin/rake db:migrate
bin/rake db:migrate RAILS_ENV=test
```
Run rspec<br>
We should now get an error that states that we have 0 tags when we expected 1<br>
To fix this go to models/**modelname**.rb and change the writer method (tag_names=) to this:<br>
```
def tag_names=(tag_list)
	tags.create(name: tag_list)
end
```
Run rspec<br>
It should now pass<br>
Commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```
Now add one more unit test:<br>
```
describe 'two tags that do not exist' do 
	it 'adds the tags to the **modelname**' do
		**modelname**.tag_names = 'yolo, swag'
		expect(**modelname**.tags.length).to eq 2
	end
end
```
Run rspec<br>
The new test will fail as it will consider the list a single tag right now<br>
We, therefore, need to split the tags<br>
Go to /models/**modelname**.rb and edit the tag_names writer method to read this:<br>
```
	def tag_names=(tag_list)
		tag_list.split(', ').each do |tag|
			tags.create(name: tag)
		end
	end
```
Run rspec<br>
It should now pass<br>
Commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```
The problem is that we are creating new tags all the time now<br>
It would be much better if we only create new tags when they do not already exist<br>
To do this lets create another unit test:<br>
```
describe 'tag already exists' do
	let!(:existing_tag){Tag.create(name: 'yolo')}
	
	it 'reuses the existing tag' do
		**modelname**.tag_names = 'yolo'
		expect{ **modelname**.save! }.not_to raise_error
		expect(**modelname**.tags).to include existing_tag
		expect(Tag.count).to eq 1
	end
end
```
Run rspec<br>
This test will fail<br>
First lets validate the uniqueness of our tag<br>
Go to /models/tag.rb and add:<br>
```
validates :name, uniqueness: true
```
Run rspec<br>
We are no longer creating duplicate tags but the test will fail as it will not save<br>
To fix this test we simply need to edit the tag_names writer method to read:<br>
```
	def tag_names=(tag_list)
		tag_list.split(', ').each do |tag|
			tags << Tag.find_or_create_by(name: tag)
		end
	end
```
or we can improve this method by making commas optional using a regular expression, like this:<br>
```
	def tag_names=(tag_list)
		tag_list.split(/\s,?/).each do |tag|
			tags << Tag.find_or_create_by(name: tag)
		end
	end
```
Run rspec<br>
The unit test should pass but the feature test will still fail<br>
To fix this you need to show the tags on the index.html.erb, add this:<br>
```
<p><% **modelname**.tags.each do |tag| %>
	<%= link_to tag.name, '#' %>
<% end %></p>
```
Run rspec
It should now pass<br>
Commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```




[Will Allen, Painted Ltd]:https://github.com/painted