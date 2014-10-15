##Stripe (Payment Processing)<br>
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
We will first want to set up an admin account to be able to view the payments<br>
Create admin_feature_spec.rb in the /spec/features folder<br>
Add this:<br>
```
require 'rails_helper'

describe 'admins' do

	it 'cannot sign up' do 
		visit '/admins/sign_up'
		expect(page).not_to have_content 'sign up'
	end

end
```
Run rspec<br>
You will have a failure due to routes<br>
To fix this go to the terminal and run this:<br>
```
bin/rails g devise Admin
bin/rake db:migrate
```
Run rspec<br>
The test will now fail but so will other ones<br>
To fix the ones that were passing before we need to go to config/routes.rb<br>
We need to take the line devise_for :**devisemodel** and put it above the new admin line<br>
Run rspec<br>
The old tests should pass and the new one should be the only one failing<br>
As devise has sign up as a default, it is failing<br>
To fix this go to /models/admin.rb and remove :registerable<br>
Run rspec again and we will now have a routes failure again<br>
Lets now change the test to read this:<br>
```
	it 'cannot sign up' do 
		visit '/admins/sign_up'
		expect{visit '/admins/sign_up'}.to raise_error
	end
```
Run rspec<br>
It will all pass now<br>
As long as it is all working commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```
Now we have a problem that we can not register an admin<br>
To do this we need to go to db/seeds.rb and add:<br>
```
Admin.create email: 'a@test.com', password: '12345678', password_confirmation: '12345678'
```
Now from the terminal you will need to run:<br>
```
bin/rake db:seed
```
and you will need to restart the rails server:<br>
```
control c     (control button)
rails s
```
As long as you are able to login now as an admin you should probably push to github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```
Now lets create an order_feature_spec.rb file in the /spec/features folder<br>
Add this test:<br>
```
require 'rails_helper' 

describe 'orders page' do 

	let(:**modelname**){**Modelname**.new(title: 'Test')}
	let(:**devisemodel**){**Devisemodel**.new(email: 'b@test.com', password: '12345678', password_confirmation: '12345678')}
	let(:admin){Admin.new(email: 'a@test.com', password: '12345678', password_confirmation: '12345678')}

	context 'logged in as an admin' do
		before do 
			login_as admin, scope: :admin

		end
		context 'no orders' do 
			it 'sees a message' do 
				visit '/orders'
				expect(page).to have_content 'No orders yet'
			end
		end
	end

	context 'not logged in as an admin' do
		it 'will prompt to sign in' do 
			visit '/orders'
			expect(page).to have_content 'Sign in'
		end
	end
end
```
Run rspec<br>
You will get a route failure<br>
Go to /config/routes.rb and add:<br>
```
resources :orders
```
Run rspec<br>
We now need a controller for orders<br>
Do this in the terminal:<br>
```
bin/rails g controller orders
```
Run rspec<br>
There is a problem now with the index for the order controller<br>
Go to /controllers/orders_controller.rb and create an index method:<br>
```
before_action :authenticate_admin!

def index

end
```
Run rspec<br>
We now see that it is missing the template<br>
In /views/orders/ create show.html.erb with the following info:<br>
```
No orders yet
```
Run rspec<br>
The tests should pass now<br>
As long as it is all working commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```
Now lets create a new test so the orders page is dynamic<br>
Go to spec/orders_feature_spec.rb and add this test with the logged in as admin context:<br>
```
context 'an order' do
	before do
		Order.create **modelname**:**modelname**, **devisemodel**:**devisemodel**
		visit '/orders'
	end
	it 'should display title of **modelname**' do 
		expect(page).to have_content 'Test'
	end
	it 'should display the customer email' do
		expect(page).to have_content 'b@test.com'
	end
end
```
Run rspec<br>
Now we will have an uninitialized constant Order failure<br>
Lets fix that by generating a model for order:<br>
```
bin/rails g model Order **devisemodel**:belongs_to **modelname**:belongs_to
bin/rake db:migrate
```
Run rspec<br>
Now we do not have the view set up<br>
Got to controllers/orders_controller.rb and in the index method add:<br>
```
@orders = Order.all
```
Now in /views/orders/index.html.erb edit it to look like this:<br>
```
<div class='container'>
	<% unless @orders.any? %>
		No orders yet
	<% else %>
		<% @orders.each do |order| %>
			<p><%= order.**modelname**.title %></p>
			<p><%= order.**devisemodel**.email %></p>
		<% end %>
	<% end %>
</div>
```
Run rspec<br>
As long as it is all working commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```
Now lets add the stripe gem in the gemfile:<br>
```
gem 'stripe', :git => 'https://github/stripe/stripe-ruby'
```
In the terminal we need to do a bundle install:<br>
```
bundle
```
We now need to go to https://stripe.com and sign up<br>
Once you are signed up, sign in and click on Your account + Account settings + API Keys<br>
Copy the keys in to your secrets.yml file:<br>
```
stripe_secret_key:  Secret key goes here
stripe_public_key:  Public key goes here
```
Now we need to create stripe.rb in /config/initializers/ with the following information:<br>
```
Stripe.api_key = Rails.application.secrets.stripe_secret_key
```
Now go to config/routes.rb and edit the **controllername** resources to look like this:<br>
```
resources :**controllername** do 
	resource: map (only need this if you are using maps)
	resources: charges
end
```
Now we need to create a charges controller in the terminal:<br>
```
bin/rails g controller charges
```
Now in controllers/charges_controller.rb add this (taken from https://stripe.com/docs/checkout/guides/rails then edited):
```
	def new
		@**modelname** = **Modelname**.find params[:**modelname**_id]
	end

	def create
		@**modelname** = **Modelname**.find params[:**modelname**_id]
		# Amount in cents
	  	@amount = 2000

		customer = Stripe::Customer.create(
			:email => params[:stripeEmail],
		    :card  => params[:stripeToken]
		  )

		charge = Stripe::Charge.create(
		    :customer    => customer.id,
		    :amount      => @amount,
		    :description => 'Print of #{@**modelname**.title}',
		    :currency    => 'usd'
		  )

		Order.create(**devisemodel**: current_**devisemodel**, **modelname**: @**modelname**)
		flash[:notice] = 'thanks for your payment'
		redirect_to **controllername**_path
	rescue Stripe::CardError => e
	  	flash[:error] = e.message
	  	redirect_to charges_path
	end
```
Now in views/charges/ create the file new.html.erb with the following info:<br>
```
<div class='container'>
	<%= form_tag **modelname**_charges_path(@**modelname**) do %>
	  <article>
	    <label class="amount">
	      <span>Amount: $5.00</span>
	    </label>
	  </article>

	  <script src="https://checkout.stripe.com/checkout.js" class="stripe-button"
	          data-key="<%= Rails.application.secrets.stripe_public_key %>"
	          data-description="A month's subscription"
	          data-amount="500"></script>
	<% end %>
</div>
```
Make sure to restart the rails server:<br>
```
control c   (control button)
rails s
```
Now if everything is working you can customize by making the amounts etc. dynamic<br>
As long as it is all working commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```
Lets now add some extra information to the order<br>
We should do this by writing a test for the additional information<br>
In the order_feature_spec.rb file within the context an order edit the before to look like this:<br>
```
	before do
		login_as admin, scope: admin
		new_years = Date.new(2014, 01, 01)

		Order.create(id: 1, **modelname**:**modelname**, **devisemodel**:**devisemodel**, created_at: new_years)
		visit '/orders'
	end
```
Then add this test to the context:<br>
```
it 'creates an order number' do
	expect(orders.first.number).to eq '1401010001'
end

it 'displays an order number' do 
	expect(page).to have_content '1401010001'
end
```
These tests will fail as the order number is not getting created nor displayed<br>
To fix this lets set up a method to create an order number<br>
Go to models/order.rb and add the method for numberL<br>
```
def number
	date_section = created_at.strftime('%y%m%d')
	number_section = "%04d" % id

	date_section + number_section
end
```
Run rspec and the first test will pass<br>
This will create an order number but it is still not displaying it<br>
We now need to display it<br>
Do this by editing /views/orders/index.html.erb to look like this:<br>
```
<div class='container'>
	<% unless @orders.any? %>
		No orders yet
	<% else %>
		<% @orders.each do |order| %>
			<p><%= order.**modelname**.title %></p>
			<p><%= order.**devisemodel**.email %></p>
			<p><%= order.number %></p>
		<% end %>
	<% end %>
</div>
```
Run rspec and everything should be working<br>
As long as it is all working commit to Github:<br>
```
git add .
git commit -m 'place your commit message here'
git push
```