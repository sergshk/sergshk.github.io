---
layout: post 
title:  "Python for a quick prototyping"
---
Lately I was looking for a simple app to help me manage priorities 
on my todo list, and when I realized that there is no app which could 
satisfy me I've decided to create my own. So what did I need from such 
app is simply organize data output, in other words print lines of data 
in a particular area of the window and some colors to distinguish 
things. I already have script which I am using to enter data and I am 
fairly happy with it. Almost forgotten, another requirement that I had 
back then is app has to be GUI form and work under Linux. 

I've started looking on what will get me up and running as fast as I can, 
because surely if you want to achieve performance you would choose 
something as close to native as possible, C++ for example, but I was not
 going to write new game and do not need performance of native app. I 
could have done it in a bash script, but then remember I wanted GUI 
form. I've decided to look into scripting languages and accidentally 
came across next tutorial ["The Python GTK+ 3 Tutorial"](http://python-gtk-3-tutorial.readthedocs.org/en/latest/index.html)
 which is describing how to use GTK+ 3 with Python. I've glanced a bit 
just to realize this is it, it's so easy to prototype something with 
Python that close to impossible to find something better. Few key 
strokes and you have GTK+ window instantiated, of course you have to 
spend some time in Glade designing window and then exporting it into 
XML, but then with just few lines of code and you have working 
application. I was impressed, but it does not stop there, Python is 
cross platform so is GTK. It is also easy to integrate with some of 
Ubuntu elements, in fact there are a lot of apps written in Python for 
Ubuntu.

Well unfortunately it's not all about positive sides there
 are drawbacks, probably biggest one that it require interpreter to 
execute and then there is Python's syntax, I've realized that I really 
hate syntax, you also need a good editor to overcome most obvious error 
when writing Python app - "Indentation Error". I had few of those in my 
very simple app, that required me to review my vim config and adjust it.

So here we go, I've started with no Python knowledge what so ever, and 
then glancing through tutorials in 2 hours I have nice GTK+ app just 
what I was looking for.

In conclusion I could say that 
it was a lot of fun to learn something totally new and Python is 
probably one of the best scripting languages for quick prototyping of 
GUI applications, it's irreplaceable if you want quickly throw something
 together especially under Linux, it also cross platform, so you could 
use same code on different OS, with some modifications of course, syntax
 of Python is not something I would really enjoy, but after all syntax 
does not really matter a lot.
