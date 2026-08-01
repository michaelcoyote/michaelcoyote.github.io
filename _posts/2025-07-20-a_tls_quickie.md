---
layout: post
title: "A TLS quickie"
description: "Writing a bare socket TLS client and server in Python in a few hours."
category: tech 
tags: [coyote,tech,python,cryptography]
---


[This one](https://github.com/michaelcoyote/code_examples/tree/master/py/tls) was just a quick project to see if I could put together a bare socket server
and client with TLS in python without too much trouble. I also wanted to see how the
TOML library works (it works fine and it's simple).  

Given that I wanted to see how quickly I could go from an empty file to a working server
and client, there's a lot of rough edges and very little error handling in this code.  

I based the client and server on Python 3.13 and it's TLS implementation which is the
default on this system.  Changes were made in the Python 3.8 TLS that changed the way
TLS is implemented on a socket.  The old way uses a bare `ssl.wrap_socket()` call while
the new way requires that you set up the `ssl.SSLContext` object first.  

The server and client use a self-signed certificate and we stick the certificate into
the context `context.load_verify_locations()` to trick the client into thinking it is
a trusted certificate. This is a workaround for not adding the CA to the trust store.  

The SSL module docs recommend using a context handler to set up the sockets so I just
followed that example but I wasn't sure how to best handle exception. It might be a
worthwhile exercise to improve the error handling. Some ideas on this are to simply
follow the `with` block with an `except` or `except` / `finally` block as outlined in
this [discussion post](https://discuss.python.org/t/try-with-resources-elegant-handling-of-exceptions-in-context-managers/86956/4).  

The biggest troubleshooting challenge was remembering that I needed to use the CN/SAN
name as my hostname so the certificate would validate. This took longer to figure out
than I care to admit, so note to anyone who runs across this, make sure your CN/SAN name
matches your hostname and that you use the hostname as your server address in the
`socket.bind()` configuration.

The code itself came together very quickly and there's not much to it.  Mostly
my time was spent working out the new way of setting everything up.  The server
and client code can be found [on my github](https://github.com/michaelcoyote/code_examples/tree/master/py/tls) if there's interest.

## Generating a self-signed certificate

```bash
# Generate CA certificate and key. If you're using this for some kind of
# internal infrastructure, make sure you keep the key safe and secure.
openssl req -x509 -new -nodes -newkey rsa:2048 -keyout myCA.key -sha256 -days 1825 -out myCA.crt -subj /CN='localhost ca'
# Generate server certificate request. You are also adding the SAN as some ssl
# implementations (notably Golang) require SAN and ignore CN as it is deprecated.
# Note we are getting a localhost.key and localhost.csr file out out this step
openssl req -newkey rsa:2048 -nodes -keyout localhost.key -out localhost.csr -subj /CN=localhost -addext subjectAltName=DNS:localhost
# sign the server certificate using the CA certificate and key
openssl x509 -req -in localhost.csr -copy_extensions copy -CA myCA.crt -CAkey myCA.key -CAcreateserial -out localhost.crt -days 365 -sha256
```  
  

Optionally we can add myCA.crt to the trust store.  On Linux it looks something like this:  

```bash
sudo cp myCA.crt /usr/local/share/ca-certificates/
sudo update-ca-certificates
```
  
Other platforms have different procedures.

I didn't do this because this was a temp key that I'll get rid of.  If I were
more organized I'd set up my own CA for testing, but right now I keep losing
the CA key and certificate and having to generate new ones anyway.  Some folks
use EasyDNS to make their own self-signed certs for testing but I'm fine not doing that.

## TODO  

If this were a real project I would have fixed these / Here are all the shortcuts I took to stand this up quickly  
- Wrap key and cert file locations in logic to insure everything exists before
  attempting to start the server.
- Wrap cafile and `context.load_verify_locations()` in logic to skip the setting if the file doesn't exist and use the system CA.
- `except` blocks for the `with` blocks to handle errors.
- add connection/tls logging

## Conclusions and observations  

This was a lot of fun.  I knocked this out in about a day or so while doing other things.  
About 4 hours total maybe?  Mostly just to see if I could and partially to better understand
sockets and TLS. Working with bare sockets was a learning experience as I've not used them
before and I got to see some of the implementation details that I've missed by using higher
level abstractions. 
 
While the socket library is very easy to use I got to see how easy it is to write a badly
behaved client and leave connections hanging open and end up with messy / misleading messages in the console.

I might give this a try in Rust and possibly JS as a comparison. I'd also like to try an mtls client/server.

As mentioned before the TOML module works fine. It takes a file and spits out a
dictionary. Simple as.  



## References
Docs I used or otherwise found interesting..
- [Python SSL module docs](https://docs.python.org/3/library/ssl.html)
- [Python TOML library](https://pypi.org/project/toml/)
- [Python socket module HowTo](https://docs.python.org/3/howto/sockets.html)
- [Python exception handling](https://docs.python.org/3/tutorial/errors.html)
- [generating CA and signing certs](https://serverfault.com/questions/1156946/how-to-properly-create-ca-certificate-and-sign-the-ssl-tls-cert-to-use-in-apache)
- [self signed certs with Ansible](https://www.jeffgeerling.com/blog/2017/generating-self-signed-openssl-certs-ansible-24s-crypto-modules)


