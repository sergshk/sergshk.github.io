---
layout: post 
title:  "Exploring Jekyll"
---

Today I have decided to try new way of blogging, it's GitHub pages, that use Jekyll behind the scene to generate content.

After reading some documentation and hacking couple examples together, I've decided to move my entire blog to Jekyll for simplicity. It's way simpler than using whole fledged platforms like Blogspot, all you need is internet and console to write your content.

Additionally I just want to share few lessens learned that are not necessarily clear from documentation: 
<!--more-->
- First off you don't have to install Jekyll locally in order to turn GitHub Pages into your next blog, unless you want to text your pages prior to committing them. GitHub Pages would automatically built your files upon commit, however it takes time, so don't expect to see results immediately upon commit, usually those are available in 30 sec or so.
- Second note is on pagination. Jekyll's documentation states that pagination is only available on HTML pages, my experimentation confirmed that it is available only on index.html page, under any folder. Most importantly make sure there is no permalink on that index.html otherwise it will break. In addition to pagination you can define limits on "for" loops.
- Last "gotcha" is that if you'd like to modify "head" section of your HTML code you have to grab relevant portion of the code for your theme off the GitHub project for that theme. 

After all it's very flexible way of blogging and I like it!
