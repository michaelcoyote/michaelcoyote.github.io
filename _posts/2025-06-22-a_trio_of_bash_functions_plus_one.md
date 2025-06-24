---
layout: post
title: "A trio of bash functions (plus one)"
description: "Testing out concourse-ci with fossa"
category: 
tags: [coyote,tech,bash,functions]
---




Infamous internet user [Mike Hoye](https://exple.tive.org/blarg/) had an [excellent prompt question](https://mastodon.social/@mhoye/114592067766102376) and it got me thinking about the bash functions I use the most.  There are three file and directory functions that live in my `~/.bash_functions` file and a helper function I'm documenting here for myself and others. 

Another purpose of writing this up is to think about how I use the system and how I create functions.  I think for the longest time I would try to build out every use I could think of but would get hung up in details and when I did finish in general I'd use one. I changed my thinking around this and do quick hits that fill the one need right away and iterate around that **if needed**.  Often it's not, or if a change is needed it's something I think of after years of using that tool and sometimes after work habits have changed.  These functions are not the most complete, but they're very useful ones written around this way of thinking.



## `mkcd`
This simply create a directory and `cd`s into it.  Nothing fancy but I use it all the time. Maybe I could put error handling in here but I've never needed it so far and I've been using this function for years.

```bash
mkcd () 
{ 
  mkdir -p "$@" && cd "$@" || exit; 
}
```


## `dlhere`

I worked out this command two years ago and only recently turned it into a function.  It moves files that have been downloaded in the last 60 minutes to the current directory. I used it all the time by calling from the <Ctr>-R reverse history search so it was time to make it a function.  Again, at some point I might make the time and/or directory an argument but I haven't really needed it.

```bash
function dlhere()
{
    find "$HOME"/Downloads -cmin -60 -type f -exec mv {} ./ \;;
}
```


## `dspace`

`dspace` is the most recent function I wrote and something of a companion to `dlhere` because I often end up downloading with files with spaces and it's a pain to manually rename them.  I do use the `ask` function to confirm the rename.  I like the confirmation and I will probably use it again.  I might also extend the function to allow a custom replacement character, but maybe not.  This works pretty well for me as is.

```bash
function dspace() 
{ # replace spaces in a filename with underscores
    despaced=$(echo "$1" | sed 's/ /_/g')
    if ask "\nRename $1 to $despaced?"; 
        then mv "$1" "$despaced"; 
    fi
}
```

## `ask`

The `ask` function is a helper function that I found somewhere back in the days before people putting their dotfiles on github and instead put them on their university web pages.  It's a simple function that wraps bash `read` and gives a nice clean return value and looks nice when you use it in a function.

```bash
function ask()
{
    echo -n "$@" '[y/n] ' ; read -r ans
    case "$ans" in
        y*|Y*) return 0 ;;
        *) return 1 ;;
    esac
}

```

