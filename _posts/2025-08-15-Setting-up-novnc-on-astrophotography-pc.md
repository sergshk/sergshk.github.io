---
layout: post 
title:  "Setting up noVNC on mini-pc for my hobby, why not?"
---

## Preamble, or few words about hobby:
Not every one knows about my night time hobby, everyone just assumes that people in cybersecurity never sleep. Well let me disappoint you we don't just sleep, but there is always a time for a hobby, otherwise it's easy to go crazy. So with that in mind couple years ago I've stumbled into a my childhood hobby with a twist. 
I loved astronomy back in my high school days and been successful in what in US called UIL competition, however when it was time to graduate I turned down admittance into university on astrophysics and went through an entrance exams into technology university. As you would realize by now my "new" hobby is somehow connected to astronomy, well it's  astrophotography- art of photographing night sky and capturing deep sky objects through a long exposure.
![Picture of Flame and Horse Head Nebulae](/assets/horse_head_nebula.jpg)
One of my pictures...
<!--more-->
You would ask how this prelude is connected to technology in any way, after all it has to be technology blog post. Simple answer people are lazy, and just like any lazy person would do I had everything automated, as much as I could, and this is what that story is all about. 
My astrophotography setup had small computer called Raspberry Pi that drives all automation for taking long exposures. After using for few years Debian release that Raspberry Pi was running was no longer supported and I've decided to upgrade, however I also decided to swap Raspberry Pi to mini-pc.  First problem that since that old Debian version has lot's of nice bells and whistles, upgrade would destroy everything since development of those tools stopped long time ago and community never ported it in modern versions. Well I figured that at the backend it uses other open source tools and those old packages are nothing more than just configuration scripts. So I chosen Ubuntu as a trusty workhorse and to be able to connect from any device in the field(you have to travel into a dark place due to light pollution in cities) I've decided to setup noVNC.
What is noVNC - it's an open source project that effectively (unlike it's name suggest) provides VNC connection over websocket, well technically noNVC bit more than that, but we'll be taking only part that provides what I've described. You'd be asking why not just use VNC, well with noVNC you can connect just from your browser and you don't have to have any app install, and what if you forget to install app before you trip and now you are travelled to dark site and there is no reception, or even if you forgot your phone, your phone died or something with noVNC you can use any device with browser no other tools needed. Technically with modern latency of e-ink screens you can even use your e-book reader. 
## The Plan
Well where do we start? I'd typically recommend Ubuntu as easiest distro, however Ubuntu has Wayland now, which adds complication so I went with Xubuntu, that is less stressful on a hardware and still uses XServer.
Here is very simple plan on what we going to achieve:
- Prerequisite: Install Xubuntu
- Install necessary packages VNC, noVNC, websockify
- Configure VNC
- Setup certificates
- Launch websokify
- Bonus: Turn it into a service
## The Action
Assuming you've already installed Xubuntu, if you have no idea how to do that find tutorial online, all you need is just bootup from USB drive(image available online) and follow simple steps.

After installing Xubuntu, it's time to install tigervnc, actual VNC that would do all work. Plus all other packages that needed for that setup.
```bash
sudo apt install tigervnc-standalone-server xfce4-goodies python3-pip python3-websockify python3-numpy novnc 
```
Setup VNC password and answer "n" for view only password, unless you need that.
```bash
vncpasswd
```
Typically this action will create configuration folder with password file called "passwd". Typically that folder will be called ".vnc" in your home folder however if you didn't find it there you need to find it using find command
```bash
find ~ -name "passwd"
```
Some Xubuntu based releases might have it under for example ~/.config/tigervnc/ however in my case folder matched was ".vnc" in my home folder. Next create file xstartup in that folder {PATH_TO_YOUR_CONFIG_FOLDER}/xstartup in your favorite editor like in my case:
```ssh
vim ~/.vnc/xstartup
```
Add next lines into the file, I won't go into explaining what those lines are for, if you are interested read docs:
```bash
#!/bin/sh
xhost +local:
xrdb $HOME/.Xresources
unset SESSION_MANAGER
dbus-launch --exit-with-session startxfce4
```
Make this file executable, pay attention to path, yours might be different:
```bash
chmod +x ~/.vnc/xstartup
```
Let's test that server actually starts:
```bash
vncserver -localhost no :1
```
You suppose to see something along the lines:
```
New Xtigervnc server 'yourhostname:1 (yourusername)' on port 5901 for display :1.
Use Xtigervncviewer -SecurityTypes VncAuth,TLSVnc -passwd {your_conf_folder}/passwd yourhostname:1 to connect to the VNC server.
```
Test it if you are unsure with command:
```bash
vncserver -list
```
You will see something like:

![Screenshot of results execution of vncserver -list command on linux machine](/assets/vnc_list.png)

Now it's time to setup novnc itself
Generate certificate, filling details into interactive prompts, I won't be walking through that part, just command:
```bash
openssl req -x509 -nodes -newkey rsa:3072 -keyout novnc.pem -out novnc.pem -days 3650
```
Final step, kill VNC process, restart opening it for localhost, since we will have websockify running on localhost, and then run websockify.
```bash
vncserver -kill :1
vncserver :1
/usr/bin/websockify -D --web=/usr/share/novnc/ --ssl-only --cert=/{folder_where_you_created_cert}/novnc.pem 8080 localhost:5901
```
Now you can access it on the port you've assigned to websockify to listen to in example above port was 8080. Just head to that IP and test that it's working.
http://{IP_OF_YOUR_MINIPC}:8080/vnc.html

Bonus let's turn all of that into a service.
Create service file:
```bash
sudo vim /etc/systemd/system/vncserver@.service
```
Add this to that file:
```
[Unit]
Description=Start TigerVNC server at startup
After=syslog.target network.target

[Service]
Type=forking
User=username
Group=username
WorkingDirectory=/home/username

PIDFile=/home/username/.vnc/%H:%i.pid
ExecStartPre=-/usr/bin/vncserver -kill :%i > /dev/null 2>&1
ExecStart=/usr/bin/vncserver -depth 24 -geometry 1920x1080 :%i
ExecStartPost=/usr/bin/websockify -D --web=/usr/share/novnc/ --cert=/home/username/novnc.pem 8080 localhost:5901
ExecStop=/usr/bin/vncserver -kill :%i

[Install]
WantedBy=multi-user.target
```
Next enable service:
```bash
sudo systemctl daemon-reload
sudo systemctl enable vncserver@1.service
sudo systemctl start vncserver@1.service
```

## Final thoughts, WARNING:

That is all I'd like to cover in this post, however few things to understand: my mini-pc is never online so I intentionally simplified everything, if I was planning to put it on the network, I'd create secure tunnel that has strong encryption, then would access it on localhost through the tunnel, I'll have firewall setup on that box controlling ingress and egress, effectively limiting any connectivity to just tunnel, strong authentication, proper setup of user privileges and finally I'd run through my penetration testing routine to ensure that box is secured in my lab and THEN will allow it to connect to network. Final thought: I would never put it on the Internet ... period ... it's just asking for troubles.
Stay safe online.

P.S. Astrophotographers who are setting up remote rigs, let me know if you need any help securing your setups!
