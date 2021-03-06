---
layout: post
title:      "CLI Project:  Hard Lesson Learned Between Lesson and Real-Life Websites"
date:       2021-03-06 01:50:35 +0000
permalink:  cli_project_hard_lesson_learned_between_lesson_and_real-life_websites
---


Originally my CLI Portfolio Project started out rough. My initial idea for a gem was to scrape the Megamillions and Powerball website to allow a user to view most recent numbers, past numbers, and if the information was available far enough back, to show most common numbers (even though you would think they would be distributed evenly, but maybe not!). I don't play the lottery often but when I do, I do not particularly like going to multiple pages, websites to see the winning numbers, especially if they were past numbers.

I was pretty comfortable with the Open-URI and Nokogiri lessons and knew that many times it was just trial and error for finding the information, so I began down that path.  As I began looking at the Powerball and Mega Millions websites I began to realize that the lessons do not always translate to reality. Inspecting the HTML code, was fairly easy, but ran into two problems:

1. Even though the winning numbers were there, they were buried deep within different div's, ID's, and classes.
2. The past numbers did not have specific webpages. You could change the date range and output data without the URL changing.

I decided to start with #1 and just trying to obtain the winning numbers. As I tried multiple .css searches, nothing was coming up specifically with the numbers, even though I was getting to locations that looked like they should be there. I continued to increase the scope of my .css search, copying the results into a word doc and searching for the winning numbers via Find (Command + F). No matter what I did, I could not find the numbers in the output HTML file, even though I could see them when I inspected the website.

As I dug deeper, it became apparent that this website had a much more advanced setup that I had seen up to this point. There were references to some javascript code and databases. From what I could gather it appears they were possibly injecting the number from a database into the website's HTML, and Open-URI/Nokogiri was unable to obtain the values. I spent quite a bit of time to come to this conclusion as this was my first project using Open-URI/Nokogiri.

I did find that I could get the values if I used yahoo search engine, but I could only get the most recent as they would put the most recent at the top of the search results page.  However, I did not think this warranted a good enough project to just be able to pull the most recent numbers.

After banging my head against this problem for a while, I decided to shift gears and come up with a different topic for my project. I had found an interesting Wikipedia website that shows the anniversaries of notable historical events on any particular day (https://en.wikipedia.org/wiki/Wikipedia:Selected_anniversaries). At my current job in a manufacturing facility we have TV screens that show facts or famous quotes on the screens that I always find interesting. I thought that this would be a perfect application for a CLI project in which I could see the historical events of a particular day.

Knowing that we want the project to have some complexity to it and allow the user to have at least one level of interaction I decided that it would be best to have two options. One option would be to see today's events and another option would be to select select a month and then a day.

Wanting this to be an object-oriented program I broke this down into several different objects:
1. CLI Interface
2. Month
3. Scraper
4. Event

I debated making a Day object, but really the day is only an input coming from the user to direct them to the correct website.  There weren't really any other properties that required storing for a particular day.  On the other hand the Month did have some properties that I wanted to store. I wanted to be able to reference the month by number (e.g. January = 1, February = 2, etc.) and I also wanted to have the number of days in the month.  Storing the number of days served two purposes:

1. Logically we would not want a user to be looking for a number outside of the actual number of days in the month.
2. The websites I am parsing have the format of https://en.wikipedia.org/wiki/Wikipedia:Selected_anniversaries/**#{@month}#{@day**}.  So I would reach an unavailable website.

Either way I would want to be able to verify the input, provide a message if it was out of bounds, and re-run the initial prompt so the user can try again.

After initial setup of my programs, I got to the part where I would need to obtain the HTML and search it for the needed information. Luckily Wikipedia is not as complex as the Mega Millions and Powerball websites and after some trial and error using Pry, I was able to obtain the correct information for a single page and turn that into a hash. Unfortunately after getting the first page to work, I ran into several issues due to the fact that my program is actually pulling data from 366 web pages (365 days + February 29th = 366 days):

1. Despite similar formatting, each page had information prior to the list of "eligible events" that had been confirmed.  This information varied in size, so I could not just look in the exact same spot on every page.
2. Each day has a different number of events and I wanted to list all of the events that Wikipedia had.
3.  Not all events are in chronological order even though the initial one of two I looked at did.  From a user interface standpoint I wanted to always have my events in chronological order so the application looks more polished.

To handle problems 1 and 2 I had to make my code more dynamic. I was able to do this by using a While loop and also verifying the :year key value. I noticed that all of my correct events had an actual year entry that was correct.  The hash entries that were not correct had a string with letters.  I created a method to verify if the year has any non numerical characters in it so that I could eliminate the inaccurate entries.

```
def year_is_integer?(string)
      string.to_i.to_s == string
  end
```

My .css code returned an array, which I could use a While loop to search through incrementing the “I” method variable every time after verifying the data.

`while @doc.css('div.mw-parser-output ul li')[i] != nil`

Once all of the data was looked through the .css code would return a nil, which would get me out of the while loop.  The combination of the While loop and verifying if the year key value is an integer allowed me to correctly grab all information.  This just shows the importance of building dynamic code and working to improve your natural implementation of dynamic code.

To handle problem 3, which was that Wikipedia, though it seems they tried for the most part, still had entries that were not chronologically in order. I wanted my program to not just reflect Wikipedia, but improve the experience by having it in chronological order every time.  In the Event class, I created a “sorted” method which re-sorts the class variable @@all after each new Event entry is initialized after it is put into the @@all class variable.  This helps to always keep the events in order.


Two more decisions I made that I think helped speed up the program:
Read only the page that is asked for, rather than just automatically reading all 366 wikipedia pages and then pulling the events from the @@all class variable.  Reading the 366 pages would just be a waste of time/resources in this instance.
After the output of the events I clear the Events.all array.  There is no reason after the output to the interface to continue to store the events since the operation is always:  get date from user, read webpage, output events.

Overall even though it was not the original direction I had for this project, I was very happy with the results. People like to see what events happened on certain days such as their birthday or just learn about random history. I thought I had some features such as the chronological order that directly improve the user experience over just looking at Wikipedia.  I think some of the decisions I made from a programming standpoint were intentional in making it work quickly and not spend unnecessary time creating and/or storing unnecessary objects that did not add value to the program.

I look forward to the next phase/module of this class!
