---
layout: post
title: "my father's tools.."
description: ""
category: 
tags: [awk,tech,tools,coyote]
---
{% include JB/setup %}

### Awk and me.

My first awareness of the Awk language was when I saw my dad's copy of "The AWK Programming Language". I was about 10 and computers were for playing games. When he explained that it was for processing text, my eyes probably glazed over. 

As I started learning about computers, got a computer of my own, and installed Linux/UNIX, I remained dimly aware of Awk. While I am not a programmer, I learned my way around Perl for my own text processing needs and snippets of other things wrapped up in shell scripts (including Awk) for light duty work.
 
Fast forward to a few years ago, and I was getting tired of resorting to Perl to do any sort of text processing of note. I had a need to pull data I needed out of a report, select what I needed, do some computation and create a new report.  

I was already using Awk to extract the columns, so I added a bit of logic and regex to extract the lines I needed. With slightly more effort I was able to compute and report the data I wanted.
 
### Not your Father's Awk.

I now find myself using Awk all the time, especially to prepare data for use in spreadsheets and databases. Data often comes to me poorly formatted and Awk has been a lifesaver in cleaning up data to prepare for use. It fills a good niche between a shell one liner and a full blown script.

Awk has also been extended to be able to handle things such as networking and is now internet aware. This can be useful (especally for scraping web besed reports for content) and it's a good tool to have. And while it's a bit beyond the scope of this guide it can come in useful.

There are a number of good general tutorials on Awk out on the internet as well as many example scripts and one-liners that can be modified to meet your needs, and I encourage you to seek them out. On the other hand, I didn't really see anything along the lines of a "getting started" guide to take new users through creating an Awk one liner to process some text.

### Awk script structure

Awk is a very simple scripting language. Where there are a number of expressesions that can be evaluated and commands that can be executed, the structure of a line in an Awk script is:

    condition { action }

- The condition can be any evaluated expression (e.g. a RegEx), or it can be BEGIN or END. The condition is true if omittied.  
- The action can be a command or series of commands.

### Writing a one line Awk command

Below is an example of the sort of basic stuff I do with awk. I knocked this together in a few minutes and is a demonstration of basic functionality and how I go about writing an Awk script. I might spend a bit more time if I was doing something for work, but maybe not. YMMV.

Let's look at an average Linux system log. In this case syslog on Debian. I'll used the most recently rotated out "syslog.1" file (I've manually cut out a number of lines since it's pretty big).

I'm just going to run awk with the print command and  it will print every line in the file.  Normally I'd use a pager like less first to get a sense of the data structure, but this is for purposes of demonstration.


    michael@flaptop-deux ~/working/basic_commands $ awk '{ print }' /var/log/syslog.1  
    Jul 29 07:53:40 flaptop-deux rsyslogd: [origin software="rsyslogd" swVersion="5.8.6" x-pid="1023" x-info="http://www.rsyslog.com"] rsyslogd was HUPed  
    Jul 29 07:56:26 flaptop-deux anacron[32680]: Job `cron.daily' terminated  
    Jul 29 07:56:26 flaptop-deux anacron[32680]: Normal exit (1 job run)  
    Jul 29 08:09:02 flaptop-deux CRON[769]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete)  
    Jul 29 08:17:01 flaptop-deux CRON[835]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)  
    Jul 29 08:29:41 flaptop-deux NetworkManager[1121]: <warn> nl_recvmsgs() error: (-33) Dump inconsistency detected, interrupted  
    Jul 29 08:41:35 flaptop-deux CRON[1220]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete)  
    Jul 29 08:45:12 flaptop-deux kernel: [458502.668733] gnome-screensav[1203]: segfault at 18 ip 00007f2b7fbff970 sp 00007fff3d3e8068 error 4 in libgdk-3.so.0.400.2[7f2b7fbc8000+79000]  
    Jul 29 09:09:01 flaptop-deux CRON[1934]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete)  
    Jul 29 09:17:01 flaptop-deux CRON[2025]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)  
    Jul 29 09:39:02 flaptop-deux CRON[2215]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete)  
    Jul 29 10:05:30 flaptop-deux dhclient: DHCPREQUEST of 172.28.200.131 on wlan0 to 172.28.200.1 port 67 
    Jul 29 10:05:30 flaptop-deux dhclient: DHCPACK of 172.28.200.131 from 172.28.200.1 
    Jul 29 10:05:30 flaptop-deux dhclient: bound to 172.28.200.131 -- renewal in 30621 seconds. 
    Jul 29 10:09:01 flaptop-deux CRON[2513]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete) 
    Jul 29 10:17:01 flaptop-deux CRON[2639]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly) 
    Jul 29 10:39:01 flaptop-deux CRON[2899]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete) 
    Jul 29 11:09:01 flaptop-deux CRON[3163]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete) 
    Jul 29 11:17:01 flaptop-deux CRON[3235]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly) 
    Jul 29 11:39:01 flaptop-deux CRON[3419]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete) 
    Jul 29 12:09:01 flaptop-deux CRON[3679]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete) 
    Jul 29 12:17:01 flaptop-deux CRON[3753]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly) 
    Jul 29 12:39:01 flaptop-deux CRON[3928]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete) 
    Jul 29 13:09:01 flaptop-deux CRON[4170]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete) 
    Jul 29 13:17:01 flaptop-deux CRON[4240]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly) 
    Jul 29 13:18:01 flaptop-deux kernel: [474871.824509] CE: hpet increased min_delta_ns to 30169 nsec 
    Jul 29 13:18:01 flaptop-deux kernel: [474871.824659] CE: hpet increased min_delta_ns to 45253 nsec 
    Jul 29 13:39:01 flaptop-deux CRON[4417]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete) 
    Jul 29 14:09:01 flaptop-deux CRON[4681]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete) 
    Jul 29 14:17:01 flaptop-deux CRON[4757]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly) 
    Jul 29 14:39:01 flaptop-deux CRON[4933]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete) 
    Jul 29 15:09:01 flaptop-deux CRON[5186]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete) 
    Jul 29 15:17:01 flaptop-deux CRON[5257]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly) 
    Jul 29 15:39:01 flaptop-deux CRON[5432]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete) 
    Jul 29 16:09:01 flaptop-deux CRON[5691]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete) 
    Jul 29 16:17:01 flaptop-deux CRON[5764]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly) 
    Jul 29 16:39:01 flaptop-deux CRON[5954]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete) 
    Jul 29 17:09:01 flaptop-deux CRON[6209]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete) 
    Jul 29 17:17:01 flaptop-deux CRON[6278]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly) 
    Jul 29 17:39:01 flaptop-deux CRON[6453]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete) 
    Jul 29 18:09:01 flaptop-deux CRON[6699]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete) 
    Jul 29 18:17:01 flaptop-deux CRON[6790]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly) 
    Jul 29 18:35:51 flaptop-deux dhclient: DHCPREQUEST of 172.28.200.131 on wlan0 to 172.28.200.1 port 67 
    Jul 29 18:35:51 flaptop-deux dhclient: DHCPACK of 172.28.200.131 from 172.28.200.1 
    Jul 29 18:35:51 flaptop-deux dhclient: bound to 172.28.200.131 -- renewal in 35595 seconds. 
    Jul 29 18:39:01 flaptop-deux CRON[6978]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete)
    Jul 29 18:54:39 flaptop-deux avahi-daemon[1109]: Invalid response packet from host 172.28.200.125.
    Jul 29 19:09:01 flaptop-deux CRON[7270]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete)
    Jul 29 19:17:01 flaptop-deux CRON[7481]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
    Jul 29 19:39:01 flaptop-deux CRON[7716]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete)
    Jul 29 20:09:01 flaptop-deux CRON[7991]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete)
    Jul 29 20:17:01 flaptop-deux CRON[8067]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
    Jul 29 20:39:01 flaptop-deux CRON[8244]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete)
    Jul 29 21:09:01 flaptop-deux CRON[8505]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete)
    Jul 29 21:17:01 flaptop-deux CRON[8580]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
    Jul 29 21:39:01 flaptop-deux CRON[8752]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete)
    Jul 29 22:09:01 flaptop-deux CRON[9007]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete)
    Jul 29 22:17:01 flaptop-deux CRON[9080]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
    Jul 29 22:39:01 flaptop-deux CRON[9249]: (root) CMD (  [ -x /usr/lib/php5/maxlifetime ] && [ -d /var/lib/php5 ] && find /var/lib/php5/ -depth -mindepth 1 -maxdepth 1 -type f -cmin +$(/usr/lib/php5/maxlifetime) ! -execdir fuser -s {} 2>/dev/null \; -delete)

Let's say we want to sift through the log to find every segmentation fault and print the line.  For that you would add a search string before the print line.

    michael@flaptop-deux ~/working/basic_commands $ awk '/segfault/ { print }' /var/log/syslog.1 
    Jul 29 08:45:12 flaptop-deux kernel: [458502.668733] gnome-screensav[1203]: segfault at 18 ip 00007f2b7fbff970 sp 00007fff3d3e8068 error 4 in libgdk-3.so.0.400.2[7f2b7fbc8000+79000] 

So far I've been calling print with no options, but one of best features of Awk is that you can arbitrarily set the field separator character to anything. Normally it's space, but we can set it to colon for our example. We can then tell print to print the fifth field of the line which will give us the error message.

    michael@flaptop-deux ~/working/basic_commands $ awk -F: '/segfault/ { print $5}' /var/log/syslog.1 
     segfault at 18 ip 00007f2b7fbff970 sp 00007fff3d3e8068 error 4 in libgdk-3.so.0.400.2[7f2b7fbc8000+79000] 

The line we get from awk is not at all spreadsheet ready. We also need the time and date. Let's turn it into a csv format file. To do this quickly I'm going to separate on the colon again and print the time and date with a comma separating the time/date and the error line. 

    michael@flaptop-deux ~/working/basic_commands $ awk -F: '/segfault/ { print $1":"$2","$5}' /var/log/syslog.1 
    Jul 29 08:45, segfault at 18 ip 00007f2b7fbff970 sp 00007fff3d3e8068 error 4 in libgdk-3.so.0.400.2[7f2b7fbc8000+79000] 

This is good, but it leaves out the seconds. If we don't need seconds we can stop here, but if we want that data we will need to do a bit of extra work.

To be able to get the seconds back into our data, we will have to take the third field (in the case of our example:"12 flaptop-deux kernel") and split it so that it only returns the first part. To do this we will process the data using "split" which takes the field, a variable name and a field separator, in ours case $3, s and a space (" "). 

    michael@flaptop-deux ~/working/basic_commands $ awk -F: '/segfault/ { split($3,s," ")  
    print $1":"$2":"s[1]","$5}' /var/log/syslog.1  
    Jul 29 08:45:12, segfault at 18 ip 00007f2b7fbff970 sp 00007fff3d3e8068 error 4 in libgdk-3.so.0.400.2[7f2b7fbc8000+79000]  

