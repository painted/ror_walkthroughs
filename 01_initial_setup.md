Rails App with Postgrsql (using Instagram as example)

1. terminal:  rails new Appname -T -d postgresql (-T removes test suite, -d selects database type)
2. terminal:  cd Appname   
3. terminal:  subl .
4. add to .gitignore :   /config/secrets.yml
5. add to config/database.yml after pool: 5:   host: localhost
6. add to Gemfile:  <BR>
		group :test, :development do <BR>
			gem ‘rspec-rails’<BR>
			gem ‘capybara’<BR>
		end<BR>
7. terminal:  bundle
8. terminal: bin/rails g rspec:install
9. in .rspec change - - warnings to - - format documentation
10. create a features folder in spec dir
11. in spec/features create a posts_feature_spec.rb file with:<BR>
	require ‘rails_helper’<BR>
	describe ‘posts listing page’ do <BR>
		it ‘tells me there are no posts’ do <BR>
			visit ‘/posts’<BR>
			expect(page).to have_content ‘No posts yet’<BR>
		end<BR>
	end<BR>
12. terminal:  rspec (should fail)
13. terminal:  bin/rake db:create
14. terminal:  rspec
15. if test still fails  terminal:  bin/rake db:create RAILS_ENV=test
16. in config/routes.rb:  resources :posts
17. rspec
18. terminal: bin/rails g controller posts
19. rspec
20. in posts_controller.rb add:<BR> 
	def index<BR>
	end<BR>
21. rspec
22. in views/posts create index.html.erb
23. bin/rails s (to start server)
24. in spec/features/posts_feature_spec.rb add:<BR>
	context ‘with posts’ do<BR>
		before do<BR>
			Post.create(title: ‘Happy’)<BR>
		end<BR>
		it ‘should show the post’ do <BR>
			visit ‘/posts’<BR>
			expect(page).to have_content ‘Happy’<BR>
		end<BR>
	end<BR>
25. rspec
26. bin/rails g model post title:string description:text
27. bin/rake db:migrate
28. in posts_controller in index method add: @posts = Post.all
29. in views/posts/index.html.erb modify to: <BR> 
	<% if @posts.any? %><BR>
		<% @posts.each do |post| %><BR>
			<%= post.title %><BR>
			<%= post.description %><BR>
		<% end %><BR>
	<% else %><BR>
		No posts yet<BR>
	<% end %><BR>
30. rspec<br>
Commit to Github