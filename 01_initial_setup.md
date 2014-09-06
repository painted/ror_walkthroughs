Rails App with Postgrsql (using Instagram as example)
<br>
terminal:  
```
rails new Appname -T -d postgresql (-T removes test suite, -d selects database type)
cd Appname   
subl .
```
add to .gitignore:<br>
```
/config/secrets.yml
```
add to config/database.yml after pool: 5:   <br>
```
host: localhost
```
add to Gemfile:<br>
```
group :test, :development do
	gem ‘rspec-rails’
	gem ‘capybara’
end
```
terminal: <br>
```
bundle
bin/rails g rspec:install
```
in .rspec: <br>
```
change - - warnings to - - format documentation
```
create a features folder in spec dir and create a posts_feature_spec.rb file with:<br>
```
require ‘rails_helper’
describe ‘posts listing page’ do 
	it ‘tells me there are no posts’ do 
		visit ‘/posts’
		expect(page).to have_content ‘No posts yet’
	end
end
```
run rspec (should fail)<br>
in terminal:<br>
```
bin/rake db:create
```
run rspec<br>
if test still fails in terminal run:<br>
```
bin/rake db:create RAILS_ENV=test
```
in config/routes.rb add:  
```
resources :posts
```
run rspec<br>
in terminal run:<br> 
```
bin/rails g controller posts
```
run rspec<br>
in posts_controller.rb add:<br>
``` 
def index<BR>
end<BR>
```
run rspec<br>
in views/posts create index.html.erb<br>
in terminal:<br>
```
bin/rails s (to start server)
```
in spec/features/posts_feature_spec.rb add:<br>
```
	context ‘with posts’ do
		before do
			Post.create(title: ‘Happy’)
		end
		it ‘should show the post’ do 
			visit ‘/posts’
			expect(page).to have_content ‘Happy’
		end
	end
```
run rspec<br>
in terminal: <br>
```
bin/rails g model post title:string description:text
bin/rake db:migrate
```
in posts_controller in index method add: 
```
@posts = Post.all
```
in views/posts/index.html.erb modify to: <br> 
```
	<% if @posts.any? %>
		<% @posts.each do |post| %>
			<%= post.title %>
			<%= post.description %>
		<% end %>
	<% else %>
		No posts yet
	<% end %>
```
run rspec<br>
Commit to Github