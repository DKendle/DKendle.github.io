---
layout: post
title:      "My Rails Learning Experience"
date:       2020-12-09 06:29:20 +0000
permalink:  my_rails_learning_experience
---

This was my second time going through the rails module and honestly it was the best thing I could have done. The best way to learn is to make something, break it, research why it broke, fix it, and continue. Below are some of the things that finally hit home for me my second time around:

## Class is now in: Session
As a general overview, when you browse a website, each one of the links or buttons you click has a HTTP verb attached to it such as GET, POST, DESTROY, PUT, or PATCH. These verbs act with the paths that you have created in your routes.rb file to find the appropriate controller#action. The next step is then running those actions with our HTTP verbs in mind and then implicitly or explicitly rendering the corresponding view. 

For example, lets say you have a basic User class, routes file, and controller:
```
app/models/user.rb
  class Users
  end
	
config/routes.rb
  resources :users
	
app/controllers/users_controller.rb
def show
 @user = User.find_by(id: params[:user_id])
end
```
Because `resources :users` gives us `users#show` without having to write out the entire route like so `get '/users/:user_id', to: 'users#show'` navigating to `localhost:3000/users/:user_id '` will run the show action and then render the show view through a GET request. Assuming you coded a way to pull the users attribute information and display it; this will show the user.

That is all fine and dandy and makes sense! But let's say we created a new user with the user controllers like so:
```
app/users_controllers.rb
.....
def new
        @user = User.new
end

def create
  @user = User.new(user_params)
      if @user.save
           redirect_to :user_path(@user)
      else 
           render :new
       end
end
....
private
def user_params
params.require(:user).permit(:name, :email)
end
```
As it stands without storing that information we can't assume that user will be recognized on every page. 

**Remember: HTTP is a stateless protocol. In short, this means information is not stored and passed on for each request.**

So, how would we track this user through our application? 

![](https://media.giphy.com/media/2wZVM6cABptwvoZ4Gx/giphy.gif)

Cookies store information that allow the user to be recognized by the server throughout use of a website. Without this you would have to login for every...single....page. That would get old really fast. Thankfully Rails gives us access to a sessions method that acts very similar to a hash. This is where we can set information and persist that information through our application for different things, such as the user themselves. The sessions method interacts with and sets cookies for us so we don't have to manually set them.

So if we change the create action from above to something like this:
```
...
def create
  @user = User.new(user_params)
      if @user.save
	         session[:user_id] = @user.id
           redirect_to :user_path(@user)
      else 
           render :new
       end
end
```
We can store the id of the user we just created in the session[:user_id] key which secures the information. Rails will handle all the sessions information as one big cookie. It then serializes the key/value pairs as one big string. When a key is set this way Rails will prevent users from tampering with that information.
[This information and more can be found in the lessons here!](http://learn.co/tracks/online-software-engineering-structured/rails/auth/cookies-and-sessions)

**So...whats the point?**
* You can create state in an otherwise stateless protocol by carrying information in a cookie whose key can be used in your application for the desired outcome. 
* You don't have to set this information by hand and shouldn't so Rails gives us access to the session method that's data is stored in a BIG cookie. 
* When a key is set through sessions Rails takes this info, arranges it a certain way, changes it into a big string, and signs off on it to prevent tampering. So we can create state securely, yay!

**In the end**
This was only one of many things I ended up learning about Rails. In the future I will post a few more articles about how to use sessions and also nested routes vs nested resources (Yes, they are different.)

## Weird "Fun" Fact About Rails:
Rails is AMAZING...we knew that, but how amazing are we talking? Below is one of the things Rails just does because it's magical.

``` 
Code Example No.1
config/routes.rb
resources :books

app/controllers/books_controller.rb
class BooksController < AppicationController
end
```

If your routes have been created and your controllers set up, but you do not explicitly write out the actions in said controller Rails will automagically look for those routes, go through the actions, and render the corresponding view (if you created one). For example, with your files coded like above all the routes and their path helpers are available through `resources :books` , but you haven't explicitly coded those individual actions in the BooksController. Rails will still go through the controller and run the actions! It will then render the corresponding views, as long as they've been created. You won't have any models or anything else that we normally passes through the action, but it's still something that could make you go crazy.





