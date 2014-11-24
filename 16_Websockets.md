##Emails<br>
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
Using a websocket is an advanced way to keep pages up to date<br>
What happens is that your computer keeps a line open between it and the server<br>
Therefore if a page changes it happens immediately on everyone's local machines<br>
This is great for things like ticketing where you have finite stock<br>
As always let's start with a test<br>
In /spec/features/ create likes_feature_spec.rb and add:<br>
```
require 'rails_helper'

describe 'liking posts' do

	before do 
		bob = **Devisemodel**.create(email: 'bob@example.com', password: '12345678', password_confirmation: '12345678')
		bob.**controllername**.create(title: 'Cool Title', description: 'Hello World')
	end

	it 'initially has 0 likes' do 
		visit '/**controllername**'
		expect(page).to have link '❤ 0'
	end

end
```
Run rspec<br>
Obviously this test will fail as we haven't set up the concept of likes yets<br>
We can make this pass easily by just adding the following link in the **modelname** partial just within the **controllername** loop:<br>
```
<=% link_to '❤ 0' %>
```
This will make it pass but will not be dynamic<br>
Run rspec
As long as it is all working commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```
Now let's add another test in the same place:<br>
```
	it 'can be liked, incrementing the likes count' do 
		visit '/**controllername**'
		click_link '❤ 0'
		expect(page).to have link '❤ 1'
	end
```
Run rspec<br>
In routes.rb within resources :**controllername** do add:<br>
```
	resources :likes
```
Now we will need a like model<br>
To do this go to the terminal and run this:<br>
```
bin/rails g model Like post:belongs_to
bin/rake db:migrate
```
Now we are going to edit the link so we are using the create method in order to like a **modelname**<br>
Go back to the partial and add this to the link:<br>
```
, **modelname**_likes_path(**modelname**), method: :post, class: 'btn btn-danger'
```
Now if you go and look at the index you will see that you have a likes button<br>
If you click on it you will find that you have an uninitialized constant LikesController<br>
Let add a controller from the terminal:<br>
```
bin/rails g controller likes
```
Now in /controllers/likes_controller.rb let's create the create method:<br>
```
def create
	@**modelname** = **Modelname**.find params[:**modelname**_id]
	@**modelname**.likes.create
	redirect_to '/**controllername**'
end
```
Now if you run rspec you will see that likes is an undefined method<br>
We need to therefore set up the relationship between the model and likes<br>
To do this go to /models/**modelname**.rb and add:<br>
```
has_many :likes
```
This almost works, however, the 0 is hardcoded which means the test still fails<br>
To fix this we need to remove the hardcoded 0 and replace it with:<br>
```
#{**modelname**.likes.count}
```
Make sure you are using " " and not ' '<br>
Run rspec<br>
The test should now pass<br>
As long as it is all working commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```
We, however, are not using websockets yet<br>
There is a great gem for rails which makes using websockets much easier<br>
It is called websocket-rails and can be found at http://github/websocket-rails/websocket-rails<br>
To use it we need to go through the installation<br>
First add the gem to your Gemfile:<br>
```
gem 'websocket-rails'
```
From the terminal do a bundle install and run the installation:<br>
```
bundle
bin/rails g websocket_rails:install
```
This has created two files and added a require to application.js<br>
This won't work as it is<br>
You will need to find the line //= require_tree . and move it from where it is to the end<br>
We now need to add the following line to environments/development.rb:<br>
```
config.middleware.delete Rack::Lock
```
We will also have to stop using the default server webbrick and start using thin<br>
It is easy to add thin, simply add the following gem to the Gemfile:<br>
```
gem 'thin'
```
And run a bundle in the terminal:<br>
```
bundle
```
Now we will need to adjust the link to stop full page refreshes by using ajax<br>
Edit the link to the following:<br>
```
<=% link_to "❤ #{**modelname**.likes.count}", **modelname**_likes_path(**modelname**), class: 'btn btn-danger like-btn' %>
```
Rename javascripts/likes.js.coffee to javascripts/likes.js and delete everything in the file<br>
Now add to following to the file:<br>
```
$(document).ready(function(){
	$('.like-btn').on('click', function(event){
		event.preventDefault();

		var url = $(this).attr('href')
		$.post(url)
	})
})
```
This will update the information but will only show it if you do a manual page reload<br>
We are now going to finally use websockets to update the page<br>
The way to do this is using a trigger and a channel<br>
Go to /controllers/likes_controller.rb and add this to the end of the create method just before the redirect:<br>
```
WebsocketRails[:likes].trigger 'new'
```
Then in the likes.js just before the last two brackets add:<br>
```
var connection = new WebSocketRails('localhost:3000/websocket');

var likesChannel = connection.subscribe('likes');
likesChannel.bind('new', function(){
	console.log('Like registered')
})
```
This will work for now but we will eventually need to get rid of the hardcoded host info<br>
We also want to edit the likes create method so the line that has WebsocketRails reads like this:<br>
```
WebsocketRails[:likes].trigger 'new', { new_like_count: @**modelname**.likes.count }
```
Now back in the likes.js edit the likesChannel.bind line to this:<br>
```
likesChannel.bind('new', function(**modelname**){
	console.log(**modelname**.new_like_count)
})
```
This was only changing the information on the server and log<br>
To change this in the html go edit this last line to:<br>
```
	$('.like-btn').text(**modelname**.new_like_count)
```
This now changes all like buttons<br>
Let's fix that by adding the **modelname** id<br>
Go to /controllers/likes_controller.rb and edit the WebsocketRails line again to:<br>
```
WebsocketRails[:likes].trigger 'new', { id: @**modelname**.id, new_like_count: @**modelname**.likes.count }
```
And lets add a data attribute to the html partial, in the link add:<br>
```
, data { '**modelname**-id' => **modelname**.id }
```
And finally back in likes.js edit the last line again to include the id like this:<br>
```
$('.like-btn[data-**modelname**-id=' + **modelname**.id + ']').text('❤ ' + **modelname**.new_like_count)
```
Now this is only updating the correct item<br>
The web socket is now working<br>
You can test it using another computer by installing ngrok<br>
To do this run the following from the terminal:<br>
```
brew install ngrok
```
If you are signed up for ngrok you can now start the server with a subdomain from the terminal:<br>
```
ngrok -- subdomain sockettest 3000
```
If you are not signed up for ngrok you will have to just start the server without a subdomain like this:<br>
```
ngrok 3000
```
This allows people to access your localhost through the internet by going to the Forwarding address in the terminal<br>
This should all work<br>
As long as it is all working commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```
Remember we said that it was not a good idea to hardcode the host<br>
Now is the time we change that so we can deploy to heroku<br>
To fix that we need to go to like.js and find the line that starts with var connection and edit it to this:<br>
```
var connection = new WebSocketRails(window.location.host + '/websocket');
```
Run rspec and you will see that one of the tests is now breaking<br>
The first thing we see is that capybara is having a problem due to the fact that we need to use thin<br>
Add the following to the rails_helper:<br>
```
Capybara.server do |app, port|
	require 'rack/handler/thin'
	Rack::Handler::Thin.run(app, :Port => port)
end
```
And in the likes_feature_spec.rb we need the second test to use javascript testing<br>
To do this just before the do add:<br>
```
, js:true 
```
Run rspec<br>
Everything should pass<br>
As long as it is all working commit to Github and heroku:<br>
```
git add .
git commit -m 'place your commit message here'
git push
git push heroku master
heroku labs:enable websockets
heroku run rake db:migrate
heroku restart
heroku open
```
