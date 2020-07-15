---
layout: post
title:      "Job Journal: My First Sinatra App"
date:       2020-07-15 17:15:24 +0000
permalink:  job_journal_my_first_sinatra_app
---

This was my very first web application that was challenging at times, but just as fun learning how my program interacted with the browser. This application used Ruby, ActiveRecord, and Sinatra to build a simple, yet functional journal that could track jobs you were interested in by name, position title, date applied (if you wanted to include this), and description. These fields would be filled out by the user and when finished created a job post with all the information entered. 

I could go on and on about the different things I learned during this process, but only included a few of those findings down below.

## Connecting Users to Their Posts
I was having a problem trying to display the posts in a way where each user could see only their post history. Up until this point my code for the index action looked like the following:
	
	` ` `
	get '/posts' do
        if logged_in? 
            @posts = Post.all
            erb :'posts/index'
        else
            redirect "/login"
        end
    end
	` ` `
		
with this being the corresponding view:
		
```
		<% @posts.each do |post|%>
        <h3><%= post.company_name%></h3>
            <p><%= post.position_title%><br>
            <a href="/posts/<%=post.id%>"><button>See Post Details</button></a>
<%end%>
<hr>
<a href="/posts/new"><button>Create a Post</button></a>
<a href="/logout"><button>Log Out</button></a>
```

This allowed for all posts created to be stored in an instance variable and then iterated over in the view so that the company name and position title could be displayed for each post in the history. So what was the problem? This allowed for ALL posts to be displayed to ALL users. Joe Schmo could see Sally Mae's posts and vice versa. I played around for a bit and realized that if I could connect the foreign key for each post in it's database to their creators then everyone would have access to only their information. That code is shown below:

```
<% @posts.each do |post|%>
    <%if post.user_id == session[:user_id]%>
        <h3><%= post.company_name%></h3>
            <p><%= post.position_title%><br>
            <a href="/posts/<%=post.id%>"><button>See Post Details</button></a>
    <%end%>
<%end%>
<hr>
<a href="/posts/new"><button>Create a Post</button></a>
<a href="/logout"><button>Log Out</button></a>
```
I changed the code in the view to include a conditional that would show posts to users trying to access them if the data in it's foreign key column matched the current session's hash user id. Since signing up to my application would instantiate a new user object then store it with an unique number in it's id column I had a way to access each user instance faithfully. After a new user signed up or after a user logged in the session[:user_id] value was set this way:

```
session[:user_id] = user.id
```

Sessions allow for information about the user to be persisted through each browser request and setting the session[:user_id] like this gave me a way to make sure users only had access to their post history by using their unique identifier in the user database.

## Seed Files: Save yourself the Frustration
After rendering my login route and collecting information from the user I kept getting stuck somewhere in my post method:

```post '/login' do 
        user = User.find_by(username: params[:username])
        if  user && user.authenticate(params[:password])
            session[:user_id] = user.id
            redirect "/posts"
        else
           redirect "/login"

        end

    end
	```
	
	The above code was able to find the user and store their data into the local variable I had set as ```user``` but could not authenticate the password data that was part of the if condition. What was the issue? Human error! I wasn't using the correct passwords with the fake users I was creating through the browser session and mispelling the usernames when logging in.
	
Although I didn't use it this time I could have created a seed file to populate my databases with fake user and post data. This would have given me more control over testing my application while also having a place for me to keep track of how many of each object was being created and stored to either database. 

## Possible Future Updates:
This project might be enough to pass the minimum requirements for the Sinatra assessment I have coming up, but during testing I found big problems that I would like to fix so that I have that knowledge for future projects. 

#### More Secure Form Code
Right now there is nothing stopping someone from injecting my forms. Meaning, the forms that I have in place to collect data from users have nothing to filter out possible HTML, CSS, or SQL fragmented code that would allow someone to take information or change it without my knowledge. If my project were something contributing to a greater company like Facebook or Instagram this would be a HUGE security risk and could put hundreds of thousands of user's data up for grabs to the right person. Since I want to go into information security in the future this experience would be helpful and would give me better insight on how to create safe apps for the public.

#### Forget Password/ Delete Profile Functionality
I want to be able to play around with this project and give the user the ability to retreive thier forgotten passwords and also delete their profiles if wanted.
