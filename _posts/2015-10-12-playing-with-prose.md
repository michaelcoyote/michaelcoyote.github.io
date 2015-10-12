---
published: true
layout: post
title: playing with prose.io
description: ""
category: null
tags: 
  - tech
  - tools
  - editors
  - first impressions
---


## Playing around with Prose.io

This post is a test of a new program for me.  I'm playing around with a new web based editor for GitHub Pages called [Prose.io](http://prose.io/). 
The idea of prose is to allow access to my posts via the GitHub API and use the prose.ip editor to create and edit posts directly from the web without having to edit posts locally on a stand alone computer. 

This post is just some inital impressions of prose.io written as I think of them. 
 
So far the hosted version just seems to work.  I allow access to GitHub via the prose.ip front page, then I create a new post and go to town.
It works well enough although it would be better to prompt for a file name.  I thought I'd set it when I created the post, but it didn't seem take, so I changed the name on a commit of my work and that seemed to work OK.  
I also noticed that it gave me access to all the organizations and repos I have access too.  This would likely be too much for a non-technical user.  It's possible that in hosting this on a heroku or other similar instance provider for a custoemr or end user I could lock it down to only see one repository, however I've not played with that aspect of it.

I do notice that there's no spell check built in to Prose.  I'm used to having spell checking with my text entry via web forms and apps and even via vim, and I think I'd miss it if I used this full time. It's odd to me that it's not availble here. There may be a way to turn it on (or back on) but there doesn't seem to be a quick way to do it from the prose web GUI.

So far the only major problem I had was when the file I was editing became inconsistent.  I was playing around with the GUI and I turned the post into a draft and then back into a post and it caused a problem (somthing like invalid git SHA) when I attempted to commit.  I shut down the GUI by closing the tab and then reopened it and went to the file.  I didn't lose much data, just a line and some formatting, but it's something I'd look out for if I was going to put this in front of a customer. 

I'm still not clear on how to "publish" I do see an "unpublished" at the top right corner, but after the "drafts" vs. "posts" thing, I'm kinda not wanting to touch it without a commit first.

The publish button does set the publish metadata to true, but it dosen't actually include the needed metadata to publish. There's a place to do this by hand.  This needs to be better for a non-technical user to be able to navagate.

Ideally I'd like to see a workflow like so:  
1. Prompt for the post title
	1. create the filename from the post title
    2. put the title in the metadata
2. inital commit with the post set to unpublished
3. write with save/commit as desired
4. publish
	1. propmpt for tags
    2. prompt for other metadata like summary
    3. final commit

Overall it's a mixed first impression.  It's cool to have a tool like this avalible however I think it's a ways off from being able to put a non-technical end user in front of and allowing them to get right to work.  There may be settings in the hosted version that allow a workload I've discribed above, but I've not experiemented with it at all to say.

Another thing that I've not tested is the possiblity of using this editor with other static page generators.  This was recommended for use in conjunction with Jekyll, but I could guess it would work with other site generators with few modifications.