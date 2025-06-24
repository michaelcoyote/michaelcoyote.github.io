---
layout: post
title: "A trio of bash functions (plus one)"
description: "Testing out concourse-ci with fossa"
category: 
tags: [coyote,tech,bash,functions]
---




Infamous internet user [Mike Hoye had an excellent prompt question](https://mastodon.social/@mhoye/114592067766102376) and it got me thinking about the bash functions I use the most.  There are three file and directory functions that live in my `~/.bash_functions` file and a helper function I'm documenting here for myself and others.


### `mkcd`

This simply create a directory and `cd`s into it.  Nothing fancy but I use it all the time. Maybe I could put error handling in here but I've never needed it so far and I've been using this function for years.

```bash
mkcd () 
{ 
  mkdir -p "$@" && cd "$@" || exit; 
}
```

### `dlhere`

I created this two years ago and only recently turned it into a function.  It moves files that have been downloaded in the last 60 minutes to the current directory. I used it all the time by calling from thy <Ctr>-R reverse history search so it was time to make it a function.

```bash
function dlhere()
{
    find "$HOME"/Downloads -cmin -60 -type f -exec mv {} ./ \;;
}
```

### `dspace`

`dspace` is the most recent function I wrote and something of a companion to `dlhere` because I often end up downloading with files with spaces and it's a pain to manually rename them.  I do use the `ask` function to confirm the rename.  I like the confirmation and I will probably use it again.  I might also extend the function to allow a custom replacement character, but maybe not.  This works pretty well for me.

```bash
function dspace() 
{ # replace spaces in a filename with underscores
    despaced=$(echo "$1" | sed 's/ /_/g')
    if ask "\nRename $1 to $despaced?"; 
        then mv "$1" "$despaced"; 
    fi
}
```

### `ask`

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

