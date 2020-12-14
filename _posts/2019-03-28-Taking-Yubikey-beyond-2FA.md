---
layout: post 
title:  "Taking Yubikey beyond 2FA"
---

Security usually associated with a lot of restrictions and a lot of hurdles that you have to jump through, but does it have to be that way? Is there a something which will help us to keep that balance of sanity and still provide strong security? Fortunately there is one thing, it's called Yubikey. Company that sells it started with attempts to take hassle out of the two-factor authentication and in my opinion they successfully achieved that. However Yubikey is capable of many more things, not just 2FA, it can carry your PGP keys, certificates and so on.
![Keys picture](/assets/keys-525732_1280.jpg)
<!--more-->
I've been using Yubikey for a while now. I think as everybody else out there I got it as a strong 2FA option and set it up on all of the sites that supported it. You could find list of services <a href="https://www.yubico.com/works-with-yubikey/catalog/">here</a>. After main purpose was satisfied, my mind kept on thinking is there anything else I could use it for in order to make that small device even more useful. I wish Yubico would do a better job, explaining capabilities of Yubikey, but you can't be perfect on all aspects. And I embarked on my journey to DuckDuckGo and found really interesting <a href="https://blog.josefsson.org/2014/06/23/offline-gnupg-master-key-and-subkeys-on-yubikey-neo-smartcard/">article</a>. Apparently in addition to 2FA and static password Yubikey could be configured as a smart card which will carry your encryption, authentication and signing keys. That's the challenge, I thought to myself, let's do it.
<br />
<br />
Since it is recommended to create those keys on a safe environment I had decided to use one of the live distros without connecting to my network just to be safe. I've created USB stick with Tails, because Tails(version from end of January 2019) had all necessary tools and I don't have to worry about installing anything extra without internet connection. Just to be over paranoid I've also turned physical switch off for wireless connectivity on my laptop before booting into OS. Then just because I wanted to learn things hard way I printed document instead of saving it on a flash drive. That forced me into typing commands myself rather than copy-pasting it from a tutorial. As a side note useful reminder if you decided to use Tails you need to set password for your user so that you could use "sudo" command if necessary. After going through entire tutorial I've ended up with set of OpenPGP keys on my Yubikey: encryption key, authentication key and signature key. After that all I have to do is to trust those keys on my daily system and of course install missing software if necessary.
<br />
<br/>
So now what? I think it's time to put those keys to a good use. As a first step I have decided to configure authentication key for SSH connection and that's where I hit the wall. There are a lot of tutorials explaining how to do that, but many of them are out of date and assume that you have difference in version of gpg-agent and gpg itself. Those tutorials leading you to install gpg2 and so on, which was not necessary for me as Ubuntu 18.04 already had 2.x gpg. Even official <a href="https://developers.yubico.com/PGP/SSH_authentication/">link</a> on Yubico web site to configure SSH on Ubuntu does that and takes you through installation of gpg2. There are also a lot of tutorials for an older version of gpg-agent and for those cases many of the switches has been deprecated by now and simply throw out warning messages. So I decided to fix it and here is the <a href="https://github.com/sergshk/toolscripts/blob/master/yubi_ssh_config.sh">link</a> to my version of similar script which should work on Ubuntu 18.04. 
<br/>
<br/>
Let's dive bit deeper into the setup, everything below is for Ubuntu 18.04 with current updates as of March 2019.
<br />
<br />
What I've learner in my attempt to configure everything is that gpg-agent relies on GPG_AGENT_INFO environment variable. That variable contains socket of currently initiated agent. As such when you are trying to follow all the other tutorials that advise you to run agent you'll get a message that "gpg-agent already running no need to start new one". So let's see if you have agent already running in your system. You could do that by listing all environmental variables and limiting to those which has gpg in it:
<br />
<pre>
<span>export | grep -i gpg</span>
</pre>
You'll get something like this if your gpg-agent was initialized:
<pre>
<span>declare -x GPG_AGENT_INFO="/run/.../S.gpg-agent:0:1"</span>
</pre>
Please note that I have intentionally removed some sub folders from the real path and you would not see "...". That is common through out the rest of this article, so "..." means that there was some additional text which I decided to remove.
<br />
<br/>
Now let's go over my script section by section. First section of my script attempts to understand situation you are in and builds your configuration file for gpg. Some of the configuration preferences may look controversial, but that's my preference you may choose something else if you wish. Then script adds few configuration lines for gpg-agent itself, most important is that it enables ssh support there, because that's what we are trying to do in this article and use authentication key from Yubikey as SSH key. My recommendation is to dig through those configuration values and understand what are your requirements.
<br />
<br />
Last portion is simply redefining SSH_SOCKET variable to point it to socket from gpg-agent rather than from ssh-agent. Since we are trying to use authentication key from a Yubikey as SSH key and as such gpg-agent will have to pretend to be an ssh-agent.
<br />
<br />
Let's continue with our manual approach. Previously we've identified that gpg-agent already running and occupying socket, now let's try to see if gpg-agent was properly configured with SSH support. You could do that by this command:
<pre>
<span>gpgconf --list-dirs agent-ssh-socket</span>
</pre>
You should get something like that:
<pre>
<span>/run/.../S.gpg-agent.ssh</span>
</pre>
Again my sample output was edited.
<br />
<br />
If you see something like that, it indicates that gpg-agent is running with ssh support. Now you can just add line to redefine value of SSH_AUTH_SOCK variable and write that into your .bashrc file.
<pre>
<span>echo 'export SSH_AUTH_SOCK=$(gpgconf --list-dirs agent-ssh-socket)' &gt;&gt; ~/.bashrc</span>
</pre>

and just to update current session you'll do:
<pre>
<span>export SSH_AUTH_SOCK=$(gpgconf --list-dirs agent-ssh-socket)</span>
</pre>
<br />
That's it. Now you can test things by running command:
<pre>
<span>ssh-add -L </span>
</pre>
which will produce output like:
<pre>
<span>The agent has no identities.</span>
</pre>
That's because you have not inserted your Yubikey. Go ahead and insert it right now, and rerun "ssh-add -L" and you should get few lines of text something like:
<pre>
<span>ssh-rsa ... A_LOT_OF_TEXT ... cardno:...</span>
</pre>
As you can see I've removed actual key and card number. Basically that means that it pulled SSH key from your Yubikey. If you are not seeing "cardno:" part then it's pulling some other key that you have in your system. Most likely your ssh-agent has not been redefined. If that's the case you may want to figure out where SSH_AUTH_SOCK variable pointing to and fix your issues.
<br />
<br />
After you successfully saw your hew SSH key and confirmed that it's coming from card, you can start using it on a GitHub for example. All you have to to is take that output and copy it over to GitHub into SSH key section. After that you can use your Yubikey for authentication on GitHub.

## Bonus section

If you also would like to sign your commits on GitHub, you could do that again with signing key from your Yubikey. So in order to do that go ahead and import your public key into GPG keys section of GitHub. Once you done that you have to configure your git. I am assuming that you already configured user.name and user.email with proper values, otherwise you are not going to be able to use your Yubikey. Configuration variables that you need to setup now are: user.signingkey, commit.gpgsign, tag.gpgsign. You just have to point option user.signingkey to your signing key. You can find your signing key by executing next command (Yubikey has to be inserted):
<pre>
<span>gpg --list-secret-keys --keyid-format LONG</span>
</pre>
Among the answers you'll find the one with [S] which indicates that this is signing key. Then you put key into your user.signingkey variable. Then with logical variables commit.gpgsign and tag.gpgsign you regulate what exactly you would like to do: sign commits or/and sign tags respectively. I have both options set to true, for my case. Now every time I commit I have to insert my Yubikey and enter my PIN in order to commit code and GitHub would display nice "Verified" message next to my commit. That way people could trust that those commits coming from me and no one else posing as me.
<br />
<br />
That's it. If you are still reading those lines I'd like to say thank you for your time and feel free to shoot me any suggestions or improvements for my script.
