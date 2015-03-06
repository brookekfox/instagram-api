#Implementing the Instagram API


###Adding the necessary ingredients
First go to [Instagram API](https://instagram.com/developer/) and register you application to recevie authentication.

Go to your Rails app. In the Gemfile, add 

	gem 'figaro'
	gem 'httparty', '~> 0.11.0'
	
and then ```$ bundle install```.


Now run ```$ figaro install```. This command adds a file application.yml in the config folder, and it adds that file to the .gitignore.

![image](https://github.com/brookekfox/instagram-api/blob/master/screenshots/figaro-install.png)

Open config>application.yml, add the line


	INSTAGRAM_API_KEY: your-access-token


###Making an API call in the view

In the controller action that corresponds to the view where you want to render the API data, define some variables as follows:


	def index
		@base_url     = 'https://api.instagram.com/v1/users/search?'
		@access_token = 'access_token=' + ENV['INSTAGRAM_API_KEY'] + '&q='
		#@query
		@count        = '&count=1'
	end
	
This is just one particular call. Here the goal is to grab a user's profile picture based on their handle.
To configure the URL for other calls use [this link](https://apigee.com/console/instagram). In the console (similar to Postman), you can make calls to figure out the exact URL you'll need to render the correct data.

+ We define the variables as above so that we can insert different search queries and so that the API access token stays hidden (in the application.yml file).

You can test the above URL in your browser by navigating to 

	https://api.instagram.com/v1/users/search?access_token=YOUR_API_KEY&q=SOME_INSTAGRAM_USERNAME&count=1
	
For instance, if I put in my API key and my Instagram username, I get

![image](https://github.com/brookekfox/instagram-api/blob/master/screenshots/json.png)

From this JSON, we can see exactly how to grab the pieces of information provided.

Now, in the view where you want to render the data (in this case, index.html.erb since we defined the variables in the index controller method), we need to use HTTParty. For example, if we want to render the profile picture of a certain Instagram user, we need to make the following HTTParty call:

	HTTParty.get(@base_url+@access_token+query+@count)['data'][0]['profile_picture']
	
To actually render the image on the page, we need to use the Rails helper method ```image_tag```.

	<% query = some_instagram_handle %>
	<% prof_pic = HTTParty.get(@base_url+@access_token+query+@count)['data'][0]['profile_picture'] %>
	<%= image_tag prof_pic %>
	

There are a lot of different calls you can make to the Instagram API. The [console](https://apigee.com/console/instagram) is a great resource to use to test before you implement.