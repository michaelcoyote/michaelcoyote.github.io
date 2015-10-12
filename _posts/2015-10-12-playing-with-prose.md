---
published: true
---


## Playing around with Prose.io

This post is a test of a new program for me.  I'm playing around with a new web based editor for GitHub Pages called [Prose.io](http://prose.io/). The idea is to allow access to my posts via the GitHub API and use the prose.ip editor to create and edit posts directly from the web without having to edit posts locally on a stand alone computer. 

This post is just some inital impressions of prose.io written as I think of them. 
 
So far the hosted version just seems to work.  I allow access to GitHub via the prose.ip front page, then I create a new post and go to town.  It works well enough although it would be better to prompt for a file name.  I thought I'd set it when I created the post, but it didn't seem take, so I changed the name on a commit of my work and that seemed to work OK.  
I also noticed that it gave me access to all the organizations and repos I have access too.  This would likely bee too much for a non-technical user.  It's possible that in hosting this on a heroku or other similar instance I could lock it down to only see one repository.

I do notice that there's no spell check built in to this.  I'm used to having it with my text entry via web forms and even via vim, and I think I'd miss it if I used this full time. There may be a way to turn it on (or back on) but there doesn't seem to be a quick way to do it from the prose web GUI.

I just had my first problem.  I was playing around with the GUI and I turned the post into a draft and then back into a post and it caused a problem (somthing like invalid SHA) when I attempted to commit.  I shut down the GUI by closing the tab and then reopened it and went to the file.  I didn't lose much data, just some formatting, but it's something I'd look out for if I was going to put this in front of a customer. 

I'm still not clear on how to "publish" I do see an "unpublished" at the top right corner, but after the "drafts" vs. "posts" thing, I'm kinda not wanting to touch it without a commit first.

