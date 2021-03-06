##Geotagging and GMaps<br>
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
Lets start with a test<br>
To do this we will need to install PhantomJS<br>
Go to the terminal and run this:<br>
```
brew install phantomjs
```
Now within the test, development group of the Gemfile add:<br>
```
gem 'poltergeist'
gem 'database_cleaner'
```
You will need to do a bundle install from the terminal:<br>
```
bundle
```
Then in the /spec/rails_helper.rb add:<br>
```
require 'capybara/poltergeist'
Capybara.javascript_driver = :poltergeist
```
We will need to do one more thing before we can actually write a test<br>
Still in the /spec/rails_helper we are going to have to change config.use_transactional_fixtures from true to false<br>
This will make the tests actually write to the database<br>
Therefore we will need to clean the database after we run each test<br>
Add this just after the config.use that you just changed:<br>
```
	config.before(:suite) do
		DatabaseCleaner.clean_with(:truncation)
	end

	config.before(:each) do
		DatabaseCleaner.strategy = :transaction
	end

	config.before(:each, :js => true) do
		DatabaseCleaner.strategy = :truncation
	end

	config.before(:each) do
		DatabaseCleaner.start
	end

	config.after(:each) do
		DatabaseCleaner.clean
	end
```
Now create maps_feature_spec.rb in spec/features/ with the following information:<br>
```
require 'rails_helper'

describe 'maps' do

	context '**controllername** with addresses' do

		before do 
		bob = **Devisemodel**.create(email: 'a@example.com', password: '12345678', password_confirmation: '12345678')
		bob.**controllername**.create(title: 'Cool post', description: 'Hello world', address: '25 City Road, London') 

		it 'displays a map (needs internet)', js:true do
			visit '/**controllername**'
			click_link 'Map'

		end
	end
end
```
Run rspec<br>
In the **controllername**_features_spec.rb file add this into the basic form test:<br>
```
fill_in 'Address', with: '25 City Road, London'
```
Run rspec<br>
It won't find the field address<br>
Next in your form add:<br>
```
	<%= f.label :address %><br>
	<%= f.text_area :address %><br>
```
Run rspec<br>
It will still have problems<br>
In the terminal we need to do a migration:<br>
```
bin/rails g migration AddAddressTo**Controllername** address:text
bin/rake db:migrate
```
Now we need to permit in the controllers/**controllername**.rb by adding , :address to the list<br>
We need to create a route<br>
Go to routes.rb and edit the resources :**controllername** to this:<br>
```
resources :**controllername** do 
	resource :map   (use plural for both if you want multiple maps)
end
```
In the /views/**controllername**/_**modelname**.html.erb add a link to the map:<br>
```
<%= link_to 'Map', **modelname**_map_path(**modelname**), class: 'btn btn-success' %>
```
Now we can see if we try it out that we need a controller<br>
Do this in the terminal:<br>
```
bin/rails g controller maps
```
Now lets update the test to actually be a test by adding:<br>
```
expect(page).to have_css '.gm-style'
```
Run rspec<br>

Now the problem is there is no show method for the controller<br>
Go to /controllers/maps_controller.rb and create the show method:<br>
```
def show
	@**modelname** = **Modelname**.find params[:**modelname**_id]
end
```
Then create a show template in views/maps/ called show.html.erb and put this:<br>
```
<div class='container'>
	<h1>Our awesome map goes here</h1>
</div> 
```
Now in the /views/**controllername/_**modelname**.html.erb around the link you just made do this:<br>
```
<% unless **modelname.address.blank? %>    (just before)

<% end %>							(just after)
```
This will only put the map button when there is a map<br>
The google maps api is really extensive<br>
Therefore it is best to use gmaps.js<br>
Go to http://hpneo.github.io/gmaps/ click on download<br>
Then select all and copy info on the screen<br>
Now in /vendors/assets/javascript/ create the file gmaps.js and paste the info into it<br>
Then in application.js you nedd to add this:<br>
```
//= require gmaps
```
Now in application.html.erb you need to add:<br>
```
<script src='http://maps.google.com/maps/api/js?sensor=true'></script>
```
First go to http://hpneo.github.io/gmaps/examples.html and click on basic<br>
Copy the code on the side<br>
It will look like this:<br>
```
new GMaps({
  div: '#map',
  lat: -12.043333,
  lng: -77.028333
});
```
Now create a /views/maps/show.html.erb file and paste it into this file like this with the classes:<br>
```
<div class='container'>
	<div id="map"></div>
	<script>
		new GMaps({
		  div: '#map',
		  lat: -12.043333,
		  lng: -77.028333
		});
	</script>
</div>
```
You will now need to edit this to read:<br>
```
<div class='container'>
	<div id="map"></div>
	<script>
		var map = new GMaps({
		  div: '#map',
		  lat: -12.043333,
		  lng: -77.028333
		});
	</script>
</div>
```
Now go to http://hpneo.github.io/gmaps/examples.html and click on geocoding<br>
Copy the code on the side<br>
It will look like this:<br>
```
GMaps.geocode({
  address: $('#address').val(),
  callback: function(results, status) {
    if (status == 'OK') {
      var latlng = results[0].geometry.location;
      map.setCenter(latlng.lat(), latlng.lng());
      map.addMarker({
        lat: latlng.lat(),
        lng: latlng.lng()
      });
    }
  }
});
```
Add this within the script just below the last info<br>
Edit this code to interpolate the address like this:<br>
```
GMaps.geocode({
  address: '<%= @**modelname**.address %>',
  callback: function(results, status) {
    if (status == 'OK') {
      var latlng = results[0].geometry.location;
      map.setCenter(latlng.lat(), latlng.lng());
      map.addMarker({
        lat: latlng.lat(),
        lng: latlng.lng()
      });
    }
  }
});
```
You will have to give the map id a height in the application.css.scss file or it will not be shown<br>
To do this add:<br>
```
#map {
  height: 600px;
}
```
This will actually works but the code smells<br>
Lets improve it by using a json<br>
This is the way one would make an api<BR>
Now go the the **controllername**_controller.rb and define a show action like this:<br>
```
def show 
	@**modelname** = **Modelname**.find(params[:id])
end
```
or this if you are using titles in the parameters:<br>
```
def show 
	@**modelname** = **Modelname**.find_by_title params[:id]
end
```
Next in /views/**controllername**/ create the file show.json.jbuilder with the following:<br>
```
json.address @**modelname**.address
```
Next go back to /maps/show.html.erb and everything in the script with a jquery get request like this:<br>
```
<div class='container'>
	<div id="map"></div>
	<script>
		$.get('/**controllername**/1.json', function(**modelname**){

			var map = new GMaps({
			  div: '#map',
			  lat: -12.043333,
			  lng: -77.028333
			});
		GMaps.geocode({
			address: '<%= @**modelname**.address %>',
			callback: function(results, status) {
			    if (status == 'OK') {
				    var latlng = results[0].geometry.location;
				    map.setCenter(latlng.lat(), latlng.lng());
				    map.addMarker({
				        lat: latlng.lat(),
				        lng: latlng.lng()
			    	});
			    }
			}
			});
		})
	</script>
</div>	
```
Now do this:<br>
```
Edit this line: address: '<%= @**modelname**.address %>',
To this: address: **modelname**.address,
```
The problem here is that we have hard coded the get item<br>
To make this responsive we need to edit this to this:<br>
```
<div class='container'>
	<div id="map" data-id=<%= @**modelname**.id %>></div>
	<script>
		var **modelname**Id = $('#map').data('id');

		$.get('/**controllername**/' + **modelname**Id + '.json', function(**modelname**){

			var map = new GMaps({
			  div: '#map',
			  lat: -12.043333,
			  lng: -77.028333
			});
		GMaps.geocode({
			address: '<%= @**modelname**.address %>',
			callback: function(results, status) {
			    if (status == 'OK') {
				    var latlng = results[0].geometry.location;
				    map.setCenter(latlng.lat(), latlng.lng());
				    map.addMarker({
				        lat: latlng.lat(),
				        lng: latlng.lng()
			    	});
			    }
			}
			});
		})
	</script>
</div>
```
Now let's take everything inside the script (please remove the script tags) and move it into its own file<br>
Go to /assets/javascripts/maps.js.coffee (rename to be maps.js) file and erase everything already there<br>
Then add this:<br>
```
$(document).ready(function(){
	var **modelname**Id = $('#map').data('id');

	if(**modelname**Id){
		$.get('/**controllername**/' + **modelname**Id + '.json', function(**modelname**){

			var map = new GMaps({
			  div: '#map',
			  lat: -12.043333,
			  lng: -77.028333
			});
		GMaps.geocode({
			address: '<%= @**modelname**.address %>',
			callback: function(results, status) {
			    if (status == 'OK') {
				    var latlng = results[0].geometry.location;
				    map.setCenter(latlng.lat(), latlng.lng());
				    map.addMarker({
				        lat: latlng.lat(),
				        lng: latlng.lng()
			    	});
			    }
			}
			});
		})
	}
})
```
Run rspec<br>
This should all work.<br>
As long as it is all working commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```
This test, however, is quite difficult to read so lets improve it by adding:<br>
```
expect(page).to have_map
```
Run rspec<br>
You will see that Capybara does not understand has_map?<br>
So we will have to teach it<br>
Still in the map_feature_spec.rb file just below require add this:<br>
```
class Capybara::Session
	def has_map?
		true
	end
end
```
Run rspec<br>
Obviously this will pass as we have hard coded true in<br>
Now remove the true and add:<br>
```
has_css?('.gm-style')
```
Now make sure that when you are logged out you can still see the map<br>
If you can not that is because you are asking for authentication on the show page<br>
To fix this add this to the list of except items in the **controllername**_controller.rb file:<br>
```
:show
```
As long as it is all working commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```
Lets now just clean this up a little by creating a support folder in the spec folder<br>
Now create a map_matcher.rb file in this new folder and cut and paste from class to the second end into this file<br>
Run rspec to make sure it is all still working<br>
As long as it is all working commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```





[Will Allen, Painted Ltd]:https://github.com/painted