---
layout: post 
title:  "Insuficcient storage available message on Android phones"
---
Just to make things clear I am using Sony Xperia TL from AT&amp;T, 
so everything I am talking about here is related to that phone and 
version of Android OS I am currently using which is 4.1.2.<br />
<br />
Recently
 I've started to run into an "Insufficient Storage Available" error 
message on my Android phone. So where does that come from, since I have 
several GB's of internal storage available, I though to myself, right? 
Well that's turn out to be wrong. Apparently all your gigs of internal 
storage are irrelevant if your phone has "Phone/Device Memory" partition
 on top of "Internal Storage" partition, and in my case it's just 2GB. 
All applications by default will get installed to that partition.<br />
Since that problem was very annoying I've started to look into what can be done to avoid it. Here are few steps I found working:<br />
<b><br /></b>
<span style="color: #f9cb9c;"><b><span style="font-weight: normal;"><b>Step 1 -&nbsp; Move stuff to Internal Storage.</b></span></b></span><br />
Move
 all apps which could be moved to Intenal Storage, that will still leave
 some parts of app on "Phone Memory" partition. Go to 
Settings-&gt;Apps-&gt;All, then order by size.<br />
![App menu](/assets/app_menu.png)
After selecting particular app you would have "Move to internal Storage" button.<br />
![Move to internal](/assets/move_internal.png)
Unfortunately not every application can be moved, those which include widgets or 
services are prohibited from moving by default. There is a way to lift 
that limitation with adb for some apps. <br />
Install Android SDK and enable USB debugging<br />
<i>./adb shell pm get-install-location</i>&nbsp;
This will let you know what was the original value, usually it is 0(auto). If you'd like to change that value just execute:<br />
<i>./adb shell pm set-install-location 2</i>&nbsp;
That will set value to 2(external) which has very surprising effect and you 
would be able to move even more apps to "Internal Storage" for example I
 was able to move Google Sheets and Google Docs using that trick.<br />
After you done moving apps to internal storage just execute:<br />
<i>./adb shell pm set-install-location 0</i>&nbsp;
That will&nbsp; return everything to normal and don't forget to disable USB debugging since have it on is simply insecure.<br />
<br />
<span style="color: #f9cb9c;"><b>Step 2 - Uninstall unused apps.</b></span><br />
Apps
 you've installed abviously take space, so take a deep breath and think 
from the point of: "Do I really need that app all the time or I can 
install/uninstall it when needed?" then if answers is no, uninstall it.<br />
<span style="color: #f9cb9c;"><br /></span>
<span style="color: #f9cb9c;"><b>Step 3 - Uninstall updates and disable. </b></span><br />
These
 days every subsidized phone comes with a lot of bloat-ware which 
unfortunately you can not uninstall even though it's your phone. Isn't 
that weird? Any way you still can uninstall all updates for bloat-ware 
and disable apps in Settings-&gt;Apps menu.<br />
![Uninstall updates](/assets/uninstall_updates.png)
However if you "root" your phone you would be able to uninstall anything, including bloat-ware.<br />
<br />
<br />
<span style="color: #f9cb9c;"><b>Step 3 - Clear cache</b></span><br />
Most
 apps cache some information to have access to it without costly 
requests to online resources in order to save battery and decrease data 
consumption. Overtime that cache grows a lot. To clear cache open up 
Settings-&gt;Apps-&gt;All then open each individual app and hit 
button "Clear cache".<br />
![Clear cache](/assets/clear_cache.png)
<b> </b><br />
<span style="color: #f9cb9c;"><b><br /></b></span>
<br />
<span style="color: #f9cb9c;"><b>Step 4 - Clear data.</b></span><br />
All
 apps store some data, whether it's your login information or internal 
databases, same as it is with cache it grows over time. Unfortunately 
its not easy as it is with cache, sure there is a button for that right 
above "Clear cache" button it's called "Clear data" however hitting it 
you clear all internal information stored by particular app, as a result
 you might need to enter all previously entered data again and as a 
result it might grow again to exactly the same amount. So result from 
that is questionable for app which you are using every day and if you 
are not using them every day then please see Step 2.<br />
![Clear data](/assets/clear_data.png)
<span style="color: #f9cb9c;"><br /></span>
<span style="color: #f9cb9c;"><b>Step 5 - Final resort Factory Reset.</b></span><br />
To clear everything on your phone there is for sure button for that :) 
It's called Factory Reset and depending on your phone way to access it 
is different. Down side it will erase absolutely everything from your 
phone and reset all apps, as a result you'll get exactly the same phone 
you had after a purchase, so just like with Step 4 effect from that 
action is questionable overtime, however I saw people reporting thing 
really work for them.
