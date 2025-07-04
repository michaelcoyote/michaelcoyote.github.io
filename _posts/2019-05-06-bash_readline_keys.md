---
layout: post
title: "bash readline shortcuts"
description: "bash key combinations and shortcuts"
category: tech 
tags: [coyote,tech,keyboard,bash,readline]
---
{% include JB/setup %}

# Bash key combinations and shortcuts

Over the last few years I spent a lot of time at the `bash` CLI, often on
systems without my `.dotfiles` loaded. I wanted to get faster but I couldn't
rely on heavy customization. I put together this cheat sheet of `bash` keyboard
shortcuts to help me power through issues on all sorts of systems.  Largely
this was an exercise in learning them but I still do occasionally refer back to
this page.

The `bash` shell uses a library called GNU Readline that provides easy and 
quick CLI key combination access to `bash` history, screen movement and line
editing commands.  I've collected some of these here along with some builtin
bash shortcuts for history and other functions.  

Note: If you get stuck and need to know a Readline key combination, the command
`bind -p` will list all the keybindings and the Readline functions they call.  
    
    
<br>
## Cursor Movement

| command           | description                                             |
|-------------------|---------------------------------------------------------|
| ctl + a           | Goto beginning of command line                          |
| ctl + e           | Goto end of command line                                |
| ctl + b           | Move back one character                                 |
| ctl + f           | Move forward one character                              |
| alt + f           | Move cursor forward one word                            |
| alt + b           | Move cursor back one word                               |
| ctl + l           | Clear the screen (same as `clear` command)              |
| ctl + \] \<char\> | Move forward to next occurrence of \<char\>             |
| alt + ctrl + \[ \<char\> | Move backwards to previous occurrence of \<char\>|
      
  
<br>
## Line Editing

| command           | description                                             |
|-------------------|---------------------------------------------------------|
| alt + .           | Print the last argument (i.e. `vim file1.txt file2.txt` will yield `file2.txt`) |
| esc + t           | Swap last two words before the cursor                   |
| ctl + d           | Delete the character under the cursor                   |
| ctl + h           | Delete character before the cursor                      |
| ctl + u           | Delete everything before cursor                         |
| ctl + k           | Delete everything after cursor                          |
| alt + backspace   | Delete previous word                                    |
| ctl + w           | Delete the word before the cursor                       |
| ctl + t           | Swap the last two characters before the cursor          |
| ctl + y           | Paste (if you used a previous command to delete)        |
| ctl + z           | Place current process in background                     |
| ctl + _           | Undo                                                    |
| ctl + x + ctl + e | Open current line in `$EDITOR`                          |
  
 
<br>
## History 

| command           | description                                             |
|-------------------|---------------------------------------------------------|
| `!!`              | Run previous command (e.g. `sudo !!`)                   |
| `!cmd`            | Run previous command that begins with `cmd`             |
| `!cmd:p`          | Print last command in history beginning with `cmd`      |
| `!n`              | Execute nth command in history                          |
| `!$`              | Last argument of last command                           |
| `!^`              | First argument of last command                          |
| alt + <           | Move to the first line in the history                   |
| alt + >           | Move to the end of the input history, i.e., the current line |
| ctl + r           | Search backward starting at the current line and moving 'up' through the history as necessary |
| crl + s           | Search forward starting at the current line and moving 'down' through the history as necessary |
| ctl + p           | Fetch the previous command from the history list, moving back in the list (same as up arrow) |
| ctl + n           | Fetch the next command from the history list, moving forward in the list (same as down arrow) |
    
    
<br>
## Job Control Shortcuts

| command           | description                                             |
|-------------------|---------------------------------------------------------|
| ctl + c           | Kill current process                                    |
| ctl + d           | Exit shell (same as `exit` command)                     |
    
    
<br>
## Other Misc. Keystrokes

| command           | description                                             |
|-------------------|---------------------------------------------------------|
| `~[TAB][TAB]`     | List all users                                          |
| `$[TAB][TAB]`     | List all system variables                               |
| `@[TAB][TAB]`     | List all entries in your /etc/hosts file                |
| `[TAB]`           | Auto complete                                           |
| `cd -`            | Change to previous working directory                    |
| ctl + x + ctl + v | Return the shell version                                |
    
     
<br>
## References

1. [Readline Documentation](https://tiswww.case.edu/php/chet/readline/readline.html)
2. [Bash Documentation](https://www.gnu.org/software/bash/manual/bashref.html#Readline-Arguments)
3. [`bind` command](https://linux.101hacks.com/unix/bind/)
4. [GNU Readline \(Wikipedia\)](https://en.wikipedia.org/wiki/GNU_Readline)
