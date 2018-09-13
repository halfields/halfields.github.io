---
layout: post
title:      "Store Arrays in SQLite....Sortof"
date:       2018-09-13 12:30:04 -0400
permalink:  store_arrays_in_sqlite_sortof
---


I incurred a slight problem while working on my Sinatra Portfolio Project. My project was a clearinghouse for running events in my local area. Event organizers could signup and then publish running events to my list. An event could have multiple races ( 5K, 10K, half-marathon, etc). Organizers would be able to choose races from a set of check-boxes. Selected races go into an array, which I planned to store in my database as part of my record of each event. I found that many relational database management systems allow you to store arrays in their databases, but SQLite is not one of them. 

As I got deeper into the problem, I realized one way around it was to declare a Race class to go along with my Event class. I could then set up a 'has and belongs to many' association between the two classes and create an 'events-races' join table that would hold foreign keys for both event objects and race objects. Problem solved. But this seemed a little heavy-handed. Like performing heart surgery with a shovel instead of a scalpel. I had a small set of 6 possible races for each event. Creating an entire new class, two new tables in my database, and instantiating new objects seemed like overkill, when a simple race_array for each event would have been perfect.

Well, there is a way, ...sortof. Output from the checkboxes on the new event form in views/events/new.erb is stored as an array in the params hash. The params hash is made available to the  "post  '/events'" method in the events controller. I assign this value to an array called races. I then convert the array to one long string named races_string. Strings, of course, can be stored in the database, and I include a races_string column in my database. The code looks like this:

```
  races = params[:races]
  races_string = races.join(",")
  @event.races_string = races_string
  @event.save
```

And when I want to read and show my data to a view, it is as simple as including the following code in my "get '/events/:id'" method, which takes the long string of comma separated races and converts them back into an array that can then be iterated in the show view:

	 @event = Event.find(params[:id]) 
	 races = @event.races_string || "No races listed"
	 @races = races.split(",")		
		erb :'events/show'
		
In the case where an event organizer checks no boxes, and the string is an empty string, I add the statement "No races listed".

So, to recap, arrays cannot be stored in SQLite. But take the array you want to store and change it to a string of comma separated items with the join method. When you want to show your data, take the stored string and convert it back to an array with the split method. Bim... bam... boom!
		
		
		
		

