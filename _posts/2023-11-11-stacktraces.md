---
layout: post
title: "quickly processing stacktraces"
description: "Turn a bunch of stacktraces into a file name and line number"
category: 
tags: [coyote,tech,awk,debugging,troubleshooting,stacktraces]
---

## Stacktraces

Sometimes we need to convert stacktraces from a bunch of different servers running the same binary to file names and line numbers.  This is a handy way to do it assuming the node name is at the beginning of the stacktrace line.

Grep out stacktraces from all the server log files:

```bash
grep -A20 "SIGSEGV" * > segmentation_violiations.out
```

Command to split stacktraces delimited by `--` in file:

```bash
# match --
# set up file variable: f
#   set the directory "stacktraces/"
#   use substr() to extract 7 charachters based on
#   match() which will match the string node[01][0-9]_ 
# then we append the lines to file f until a new 
#   node[01][0-9]_ is found
awk '{/--/}{f="stacktraces/"substr($0,match($0, /node[01][0-9]_/),7)"stacktrace.txt"} {print >> f }' segmentation_violiations.out
```

This will create a file with a stacktrace for each node.

Note: the split is not perfect.  We have a side effect where a `--stacktrace.txt` file full of `--` strings is created and we would likely need to remove the line prefix added by the grep command above to resolve, or we could just ignore the file.

I used `substr()` from awk here to extract the node name.  This was new to me. Here's the description from the man page:

> `substr(s, a, b)` : returns **b** number of chars from string **s**, starting at position **a**. The parameter **b** is optional, in which case it means up to the end of the string.

Run `addr2line` on everything in `stacktraces/`. You will need a binary with debug symbols, and for this case we used a container environment tomake sure we had an environment consistent with the host. We use grep to find the hex addresses in the stacktraces and then use `addr2line` to turn them into file names and line numbers.

```bash
for f in *; 
	do addr2line -i -C -f -e /usr/bin/<bin_with_objects> $(grep -Eo '\[\0x[0-9A-Za-z]{6,}\]' $f \
	   |tr -d '[' | tr -d ']' | tr '\n' ' ') > $f".backtrace" ;
done
```


