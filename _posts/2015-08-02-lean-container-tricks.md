---
layout: post
title: "lean container tricks"
description: ""
category: 
tags: [containers,tech,tools]
---
{% include JB/setup %}


# Lean Container Tricks

I would like to thank [Brian "Redbeard"](https://github.com/brianredbeard) of CoreOS who had a very good talk recently about making lightweight containers. I wanted to write this up as a summary of what he covered and a quick cheat sheet for myself and others. 

Please note dear reader that all the inspiration and info comes from Brian, and all the errors are my own. I would recommend reviewing his [presentation notes](https://github.com/brianredbeard/minimal_containers) and specifically his [README](https://github.com/brianredbeard/minimal_containers/blob/master/README.md).  I should also point out that while this procedure uses Docker, it will work with other container formats as well.

### What is a lean/lightweight container and why would I want one?

It may be helpful for us to digress a bit and explain that the basic definition of a container is a root file system that is packed into a tar archive, and "contained" usually using Linux containers (LXC) which allow an application or process to run totally within it's own environment.  For more information on containers, [this](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-getting-started) is a good place to start.

Often, container makers will create a container right out of a full OS install of a Linux distribution such as Fedora or Ubuntu.  While this will work, it will often include software components that are unnecessary for the running of a container and leads to containers that are many times larger than needed.

Another way to create containers is by creating a root directory with only the software components you need and then creating a tar file of that directory.

Some advantages to these leaner containers:  
- less storage required  
- less disk I/O   
- easier to audit  
- less updating to do when a vulnerability comes out  

These are all generally good things that will make ops and security people happy.

### How do I create a container?

The basic work flow to creating a container is simple:

Build rootfs --> Import container --> Use Container 

#### Creating the rootfs

You can use all sorts of tools to create the root FS as well.  
- [Buildroot](http://buildroot.uclibc.org/)  
- [Gentoo](https://www.gentoo.org/)  
- [debootstrap](https://wiki.debian.org/Debootstrap)  
- [yum](http://yum.baseurl.org/)/[dnf](http://dnf.baseurl.org/)  
- [Alpine](https://www.alpinelinux.org/)  
- others...   

Brian's example used [Buildroot](http://buildroot.uclibc.org/) to create a root FS and that's what I'll do here.

[Buildroot](http://buildroot.uclibc.org/) is nice for building your container because it:  
- builds software from scratch using checksummed sources  
- builds a tarball directly  
- easy menu based config  
- configuration diff file can be generated for future builds  

To build using Buildroot, [download or `git clone`](http://buildroot.uclibc.org/download.html)the software and `cd` to the `buildroot` directory.

Run the `make menuconfig` command and configure the options you want (if you really want a nice X GUI and it will make you more comfortable to do so, make sure you have X and Tk installed and then you can run `make xconfig` and you can use your mouse, there is no such thing as the mouse police). 

Note that because this is a container, you can leave out things like the Linux kernel and init systems. You won't need them at all for a container as they will piggyback off the host OS. 

Do select the language and tool options you think you will need to make your environment work. For example, you may wish to include Nginex along with your node or python installation to make your life a little easier.

When you are satisfied with your selections run the `make` command. This will take a while, so depending on the time of day and your inclination, now might be a good time for your choice of coffee, cocktail, beer or chamomile tea.

#### Importing your Container

When finished, the buildroot `make` will leave a tarball of the root image in the `buildroot/output/images/` directory.
You can import this into docker using the command:  
`cat tarroot.tar |docker import - tagname`

#### Using your Container

Note that no `Dockerfile` is needed when you create your container.  

You can start it up using `docker run -t -i tagname /bin/sh` and you will get get your container running with a shell prompt.

You can use this container as is, but to make it easier to work with, manage and track, you can create a `Dockerfile` for it.

Here is the basic `Dockerfile` as recommended by Brian.

```  
FROM tagname

CMD ["/bin/sh"]
```  

The command `docker build -t tagname:v1.0 .` should give you v1.0 of your docker image for use.

Now that your image is ready to use you can call it using the `FROM` keyword in a `Dockerfile` as you would any other container..  Use to host services, push to Dockerhub, and leave dozens of images sitting around for months after you're finished testing..
