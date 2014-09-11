##Bootstrap<br>
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
```
Go to http://getbootstrap.com and check it out<br>
For Ruby you will be using Bootstrap Sass<br>
Add the following to your Gemfile:<br>
```
gem 'bootstrap-sass'
gem 'sass-rails'    (check to make sure you have this)
```
Next in /assets/stylesheets/application.css.scss (add scss if it does not already have it) add:<br>
```
@import "bootstrap-sprockets";
@import "bootstrap";
```
now in /assets/javascript/application.js add: <br>
```
//= require jquery  (check to make sure you have this)
//= require bootstrap-sprockets
```
Now in the terminal run bundle and restart the server:<br>
```
bundle
control c  (the button control)
rails s
```
Now go to http://wwww.getbootstrap.com/getting-started/ and go down to exmaples.<br>
Click on the example you would like to use, I will be using jumbotron for this walkthrough.<br>
Now in chrome click view>developer>view source<br>
Copy all of the source code and paste that into /views/layouts/application.html.erb above the current information<br>
Now change the title and add an author and decription in the meta data<br>
Erase everything within the head after the title<br>
Now go down to the bottom, cut everything in the old head after title and paste it where you had erased the other info<br>
Next find where it says project name and change it to your project name<br>
Now you can remove all this:<br>
```


    <!-- Bootstrap core JavaScript
    ================================================== -->
    <!-- Placed at the end of the document so the pages load faster -->
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>
    <script src="../../dist/js/bootstrap.min.js"></script>
    <!-- IE10 viewport hack for Surface/desktop Windows 8 bug -->
    <script src="../../assets/js/ie10-viewport-bug-workaround.js"></script>
``` 
And remove all this (if you want to keep the jumbotron):<br>
```
      <!-- Example row of columns -->
      <div class="row">
        <div class="col-md-4">
          <h2>Heading</h2>
          <p>Donec id elit non mi porta gravida at eget metus. Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut fermentum massa justo sit amet risus. Etiam porta sem malesuada magna mollis euismod. Donec sed odio dui. </p>
          <p><a class="btn btn-default" href="#" role="button">View details &raquo;</a></p>
        </div>
        <div class="col-md-4">
          <h2>Heading</h2>
          <p>Donec id elit non mi porta gravida at eget metus. Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut fermentum massa justo sit amet risus. Etiam porta sem malesuada magna mollis euismod. Donec sed odio dui. </p>
          <p><a class="btn btn-default" href="#" role="button">View details &raquo;</a></p>
       </div>
        <div class="col-md-4">
          <h2>Heading</h2>
          <p>Donec sed odio dui. Cras justo odio, dapibus ac facilisis in, egestas eget quam. Vestibulum id ligula porta felis euismod semper. Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut fermentum massa justo sit amet risus.</p>
          <p><a class="btn btn-default" href="#" role="button">View details &raquo;</a></p>
        </div>
      </div>
```
or this if you do not want to keep the jumbotron:<br>
```
    <!-- Main jumbotron for a primary marketing message or call to action -->
    <div class="jumbotron">
      <div class="container">
        <h1>Hello, world!</h1>
        <p>This is a template for a simple marketing or informational website. It includes a large callout called a jumbotron and three supporting pieces of content. Use it as a starting point to create something more unique.</p>
        <p><a class="btn btn-primary btn-lg" role="button">Learn more &raquo;</a></p>
      </div>
    </div>

    <div class="container">
      <!-- Example row of columns -->
      <div class="row">
        <div class="col-md-4">
          <h2>Heading</h2>
          <p>Donec id elit non mi porta gravida at eget metus. Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut fermentum massa justo sit amet risus. Etiam porta sem malesuada magna mollis euismod. Donec sed odio dui. </p>
          <p><a class="btn btn-default" href="#" role="button">View details &raquo;</a></p>
        </div>
        <div class="col-md-4">
          <h2>Heading</h2>
          <p>Donec id elit non mi porta gravida at eget metus. Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut fermentum massa justo sit amet risus. Etiam porta sem malesuada magna mollis euismod. Donec sed odio dui. </p>
          <p><a class="btn btn-default" href="#" role="button">View details &raquo;</a></p>
       </div>
        <div class="col-md-4">
          <h2>Heading</h2>
          <p>Donec sed odio dui. Cras justo odio, dapibus ac facilisis in, egestas eget quam. Vestibulum id ligula porta felis euismod semper. Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut fermentum massa justo sit amet risus.</p>
          <p><a class="btn btn-default" href="#" role="button">View details &raquo;</a></p>
        </div>
      </div>
```
Replace this with:<br>
```
    <div class="container"> (this line is only necessary if you are using the second option)
    <%= yield %>
	<p class="notice"><%= notice %></p>
	<p class="alert"><%= alert %></p>
```
Now as the top nav bar is fixed you may need to add some padding to the body.<br>
In /assets/stylesheets/application.css.scss add:<br>
```
body {
	padding-top: 50px;
}
```
Check out that everything is working at localhost:3000/**controllername**<br>
Now if you have kept the jumbotron let's change the background image for it<br>
To do that go to /assets/stylesheets/application.css.scss and add:<br>
```
.jumbotron {
	background: image-url('**imagename**');
}
```
Now make sure to add an image with that name to /assets/images/<br>
Check the page and make sure that you can see the image<br>
Now you will see that the text in the image is hard to read<br>
To fix this go to application.css.scss again and in .jumbotron add:<br>
```
.container {
	background: rgba(255, 255,  255, 0.85);
	padding: 20px, 50px;
}
```
This will give your text a white background that has an opacity of .85 (85%)<br>

