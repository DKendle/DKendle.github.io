---
layout: post
title:      "My First CLI Project"
date:       2020-05-17 18:10:54 +0000
permalink:  my_first_cli_project
---


Since the first day of Flatiron I couldn't wait to start building things so when our first project was around the corner I was really excited. We were to build a CLI that would either use an API (Application Programming Interface) or would scrape a website for information. I immediately knew I was going to use an API because I wanted something that was going to be more flexible with the possible changes that happen with websites over a long period of time. This is important because I want to continue to work on this project and add to it as my journey goes on. I chose D&D as the topic and found an API with all the information currently available for the 5th edition of the game. I decided to focus on the spellcasting aspect of it and wanted to somehow pull a list of all spells then prompt the user to learn more about one of them. With project week 1 started and my rough idea in mind I began working on the framework of my project. In the process I came across 3 main issues that had me screaming, "Do it the way I want, not the way you are programmed!". 

**Problem 1: Triple Trouble**

After creating my API, CLI and Spell classes I quickly fleshed out the needed methods for my objects. The original API call was also created without any issues, but the CLI run method (controlled how the CLI works for the user) was another story. The below code is what gave me the problem because I didn't completely understand how to use a preconditional statement and only focused on what would make my code run the way I wanted. 
```
def run
    puts " "
    puts "Welcome to the Tome of Spells"
    puts " "
    puts "Is it Magic you seek: y or n"
    @input = gets.strip.downcase
		orig_input(@input)
    prompt_user if orig_input(@input)
```

I was taking the users input and using it in the following method and then prompting the user with the  CLI instance method prompt_user to enter a spell number to learn more:

```def orig_input(input)
      if input == "y" || input == "yes"
          puts " "
          puts "Very well....here are all the spells of this Universe:"
          sleep 3
          Api.spell_get_call
          puts " "
          Spell.all.each.with_index(1) do |spell, index|
            puts "#{index}. #{spell.name}"
          end
      else
          puts "(The Tome vanishes)"
          exit
        end
    end
		```
		
I thought by using a preconditional that I would be able to solve the problem of user input "no" executing the API calls as if the user had inputted "y" or "yes". Instead, it did what I expected with the added problem of executing everything three times. Which can be seen below:

```Very well....here are all the spells of this Universe:
1. Acid Arrow
2. Acid Splash
...
318. Word of Recall
319. Zone of Truth
Very well....here are all the spells of this Universe:
...
637. Word of Recall
638. Zone of Truth
Enter a 'number' to learn more about a spell; type 'list' to see the list again, or type 'exit' to close the Tome:
```
What I didn't realize was by putting the ```orig_input``` method twice (once for the CLI to run and another as a preconditional) the method would also run twice. The second time would stack the result one after another creating 2 separate lists of spells with the second one being twice as long with duplicates. 
		
		
**Problem 2: List Problems**

When coding the ```while loop``` that would allow users to pick a spell for more details, list the spells again, or exit out of the program the ```list``` portion was acting like a repeating list versus putsing the original list when prompted.

```
while prompt_input != "exit"
          if prompt_input.to_i.between?(1, Spell.all.length)
              spell = Spell.all[prompt_input.to_i - 1]
              Api.spell_info_call(spell)
              print_spell(spell)
          elsif prompt_input == "list"
              orig_input(@input)
              puts "#{index}. #{spell.name}"
							...
	```
	
	The above was putsing the entire list twice on the screen as one long list:
	
```
317. Wish
318. Word of Recall
319. Zone of Truth
320. Acid Arrow
321. Acid Splash
322. Aid
323. Alarm
....
636. Wish
637. Word of Recall
638. Zone of Truth
```
	
	
I though that I was creating DRY code by using a method that was already defined, but what I was actually doing was calling the method earlier in the CLI ```run method``` and then calling it again when the ```while loop``` had to run the ```list``` prompt. I ended up replacing the ```elsif``` to look like the below code and it fixed issue:

```
elsif prompt_input == "list"
              Spell.all.each.with_index(1) do |spell, index|
              puts "#{index}. #{spell.name}"
              end
```

**Problem 3: Git Push like your life depended on it!**

I never thought that my worst problem of this entire project would be something I have been trained to do since grade school. SAVE YOUR WORK! The recommendation is to push your changes to your github repository every 5-7 minutes of coding or every 5 to NO MORE THAN 15 lines of code, but when you are in a groove those minutes fly by. Until an internet issue happens sending you crashing back to reality with the realization that you have been working for 2 hours on your project with countless epiphanys only to have it all disappear into the ether. I lost an entire class in my program and the objects that where interacting with my API calls. Saving often will ensure that even the slightest changes won't cause you a headache later. The following code should be done often and effortlessly like breathing:
```
git add .
git commit -m "(Whatever changes you have made)"
git push
```
And check your github to ensure that all changes have been pushed without a problem!

