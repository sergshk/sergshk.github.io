---
layout: post 
title:  "Littlebit fun with Jenkins."
---
Finally I decided to write about configuring Jenkins server, which is
 absolutely must have tool for any software project. This is how project
 itself describe what Jenkins is on their own web <a href="http://jenkins-ci.org/">site</a>:

<blockquote class="tr_bq">
In a nutshell Jenkins CI is the leading
 open-source continuous integration server. Built with Java, it provides
 1009 plugins to support building and testing virtually any project.</blockquote>
That's
 not going to be a full tutorial on how to do a perfect configuration of
 Jenkins, I am targeting simplicity and as a result goal of this 
tutorial is providing quick and dirty way to set everything up and have 
it running ASAP.<!--more--> It's sort of Jenkins servers in a nutshell for busy 
entrepreneurs. Also I am going to limit this tutorial only to my system 
which is Ubuntu and I am planning to use Jenkins for project written in 
PHP, so all tools going to be for PHP as well.
Let's start with Jenkins installation. First step is to add Jenkins repository for Ubuntu and install package.
First adding key
<blockquote class="tr_bq">
<b>wget -q -O - http://pkg.jenkins-ci.org/debian/jenkins-ci.org.key | sudo apt-key add -</b></blockquote>
Then adding repo to source list:
<blockquote class="tr_bq">
<b>sudo add-apt-repository "deb http://pkg.jenkins-ci.org/debian binary/"</b></blockquote>
Finally installing package:
<blockquote class="tr_bq">
<b>sudo apt-get update</b>
<b>sudo apt-get install jenkins</b></blockquote>
All those installation steps were taken from <a href="http://pkg.jenkins-ci.org/debian/">http://pkg.jenkins-ci.org/debian/</a>

Once
 installed you would have next folder created for you /var/lib/jenkins 
that is basically default location of the Jenkins, sort of home folder 
of the server. Also it should be immediately accessible at 
http://localhost:8080/

Please type that URL into your 
browser and let's install all necessary plugins for PHP. To install 
plugins go to "Manage Jenkins"-&amp;gt;"Manage Plugins"
![Manage Jenkins](/assets/Selection_020.png)
and then open tab "Available". For PHP we would need next plugins:
<ul>
<li>Checkstyle Plugin (there are 2 versions currently available I've chosen latest)</li>
<li>Clover PHP Plugin</li>
<li>Crap4J Plugin</li>
<li>DRY Plugin</li>
<li>HTML Publisher Plugin</li>
<li>JDepend Plugin</li>
<li>Plot Plugin</li>
<li>PMD Plugin</li>
<li>Violations</li>
<li>xUnit Plugin</li>
</ul>
Select all of these and then click "Download now and install 
after restart" and on status page they conveniently offering checkbox 
"Restart Jenkins when installation is complete and no jobs are running",
 so I took advantage of that option.
![Restart Jenkins](/assets/Selection_022.png)
If installation happens way too fast it could appear that Junkins got 
stuck at few last options, what I think happened is plugins got 
installed and then Jenkins was restarted, but screen didn't get 
refreshed because previous instance of Jenkins was gone, otherwise 
you'll get something like that:
![Restarting Jenkins](/assets/Selection_023.png)
Now we have all plugins ready to go, I guess it's a time to create a first 
job, but remember goal is to get up and running as fast as we possibly 
can, so I've discovered <a href="http://jenkins-php.org/">jenkins-php</a>&nbsp;web site and turned out to Sebastian Bergmann who happened to create a template on his <a href="http://jenkins-php.org/integration.html">site</a>. Let's add that template into our setup of Jenkins and for those who are wondering I've chosen manual path:
<blockquote class="tr_bq">
<b>cd /var/lib/jenkins/jobs/</b>
<b>sudo mkdir php-template</b>
<b>cd php-template</b>
<b>sudo wget https://raw.github.com/sebastianbergmann/php-jenkins-template/master/config.xml</b>
<b>cd ..</b>
<b>sudo chown -R jenkins:jenkins php-template</b></blockquote>
Once
 that done go to "Manage Jenkins"-&amp;gt;"Reload Configuration from 
Disk". After configuration is reloaded you will be redirected to a 
dashboard where new job "php-template" going to appear. Right after that
 you could start messing with it, but I've opted to use "php-template" 
as a true template. So I've decided to create new job. Click "New Item" 
then enter a name and from radio button options choose "Copy existing 
item" and then start typing php-template in the box and you'll be 
offered it for selection. Go ahead and select php-template there. Then 
click Ok.
![Copy template](/assets/Selection_024.png)
You will be taken to job configuration script, so I've changed description 
box and hit "Save". Once it's saved you'll see name of your project and 
notice that it is currently disabled, so go ahead and hit "Enable" 
button just next to that notification. Now if you go back to the 
dashboard you will see 2 projects and "Schedule a build" icon next to 
your project. I called my project "First", so in the rest of this 
tutorial I am going to use this name for reference.
Now let's install few more tools which Jenkins would need to run a first build. Those tools are Ant and JDK:
<blockquote class="tr_bq">
<b>sudo apt-get install ant</b>
<b>sudo apt-get install openjdk-7-jdk</b></blockquote>
If
 you have several versions of openjdk installed, for instance I had 
version 7 as well as version 6 installed, please run next command and 
choose version 7 as your default:
<blockquote class="tr_bq">
<b>sudo update-alternatives --config java</b></blockquote>
So
 now let's try to run a build, which I am fairly certain that will fail.
 But first enable auto refresh of the screen to help you see what the 
status is. Hit "Schedule build" to see a very first failure, which is 
going to be:
<blockquote class="tr_bq">
<i>FATAL: Unable to find build script at /var/lib/jenkins/jobs/First/workspace/build.xml</i></blockquote>
You
 could see that message by clicking #{number} on the dashboard for your 
project which represents latest build and then choosing "Console 
Output".
![Console output](/assets/Selection_025.png)
Another positive effect we've got from running that first failure is that 
Jenkins created all necessary folders structure under your project's 
folder, remember my first project called First.

So we 
clearly need to create that build.xml file, however as I said before our
 goal is have it running as soon as we can, so we going to borrow 
build.xml from ... you guessed correctly Sebastian Bergmann, you could 
find it <a href="http://jenkins-php.org/download/build.xml">here</a>:
<blockquote class="tr_bq">
<b>cd &nbsp;/var/lib/jenkins/jobs/First/workspace</b>
<b>sudo wget http://jenkins-php.org/download/build.xml</b>
<b>sudo chown jenkins:jenkins build.xml</b></blockquote>
Now we will need to create couple more folders and add source code into appropriate place.
<blockquote class="tr_bq">
<b>cd &nbsp;/var/lib/jenkins/jobs/First/workspace</b>
<b>sudo mkdir src</b>
<b>sudo mkdir tests</b></blockquote>
Now it's a good time to copy your source code into <i>src</i> folder and your <i>tests</i> into tests folder(you could potentially leave <i>tests</i>
 folder empty, but would have to enable one extra options, I'll cover it
 later) after that we can change ownership of those files, I am pretty 
sure it's not necessary, however I just trying to avoid any unnecessary 
failures during Jenkins run.
<blockquote class="tr_bq">
<b>sudo chown -R jenkins:jenkins src</b>
<b>sudo chown -R jenkins:jenkins tests</b></blockquote>
Project is almost ready, now we need to install couple tools, because those are
 going to be used during Jenkins run. But first lest switch to some temp
 folder to avoid any mess in any other place:
<blockquote class="tr_bq">
<b>cd /tmp</b></blockquote>
First install phploc:
<blockquote class="tr_bq">
<b>wget https://phar.phpunit.de/phploc.phar</b>
<b>chmod a+x phploc.phar</b>
<b>sudo mv phploc.phar /usr/local/bin/phploc</b></blockquote>
Then install pdepend:
<blockquote class="tr_bq">
<b>wget http://static.pdepend.org/php/latest/pdepend.phar</b>
<b>chmod a+x pdepend.phar</b>
<b>sudo mv pdepend.phar /usr/local/bin/pdepend</b></blockquote>
Then install phpmd:
<blockquote class="tr_bq">
<b>wget http://static.phpmd.org/php/2.1.3/phpmd.phar</b>
<b>chmod a+x phpmd.phar</b>
<b>sudo mv phpmd.phar /usr/local/bin/phpmd</b></blockquote>
Now let's add default conf file for it, I simply took one from <a href="http://jenkins-php.org/configuration.html">http://jenkins-php.org/configuration.html </a>and placed it into&nbsp;/var/lib/jenkins/jobs/First/workspace/build/phpmd.xml

Then install phpcs:
<blockquote class="tr_bq">
<b>sudo apt-get install php-pear</b>
<b>sudo pear install PHP_CodeSniffer</b></blockquote>
Again &nbsp;I took example from same <a href="http://jenkins-php.org/configuration.html">page</a> and put it into &nbsp;<i>/var/lib/jenkins/jobs/First/workspace/build/phpcs.xml</i>

Then install phpcpd:
<blockquote class="tr_bq">
<b>wget https://phar.phpunit.de/phpcpd.phar</b>
<b>chmod a+x phpcpd.phar</b>
<b>sudo mv phpcpd.phar /usr/local/bin/phpcpd</b></blockquote>
Then install phpunit:
<blockquote class="tr_bq">
<b>wget https://phar.phpunit.de/phpunit.phar</b>
<b>chmod a+x phpunit.phar</b>
<b>sudo mv phpunit.phar /usr/local/bin/phpunit</b></blockquote>
After that we need to create a simple phpunit.xml you could find it <a href="http://jenkins-php.org/download/phpunit.xml.dist">here</a>&nbsp;once downloaded place file into &nbsp;<i>/var/lib/jenkins/jobs/First/workspace/build/phpunit.xml.</i>
 However I did few changes there, I removed all entries related to 
bootstrap.php and append all folders path with ../ otherwise it will be 
looking into wrong folders.

Just in case I have installed xdebug package since I know that xdebug is going to be invoked to generate some metrics.
<blockquote class="tr_bq">
<b>sudo apt-get install php5-xdebug</b></blockquote>
Then install phpdox:
<blockquote class="tr_bq">
<b>wget http://phpdox.de/releases/phpdox.phar</b>
<b>chmod a+x phpdox.phar</b>
<b>sudo mv phpdox.phar /usr/local/bin/phpdox</b></blockquote>
Then install xsl extension for PHP:
<blockquote class="tr_bq">
<b>sudo apt-get install php5-xsl</b></blockquote>
After that we would also need a config file for phpdox and put it into workspace/build folder:
<blockquote class="tr_bq">
<b>phpdox --skel &amp;gt; phpdox.xml</b></blockquote>
Edit phpdox.xml file and replace /src with /../src in "project" tag, remove 
/docs from "generator" tag and then change "html" to "api" in "build" 
tag. Here is quick diff:
![Diff](/assets/Selection_026.png)
After that execute next few commands to move file into place where it would be automatically picked up by Jenkins
<blockquote class="tr_bq">
<b>sudo chown jenkins:jenkins phpdox.xml</b>
<b>sudo mv phpdox.xml /var/lib/jenkins/jobs/First/workspace/build/phpdox.xml</b></blockquote>
So that's it technically you could run things and potentially will get 
successful "Unstable" build. Last thing I'd like to mention is if you 
have no test cases you better enable next option or xUnit will fail 
build.
![Skip when no test](/assets/Selection_019.png)

For the most part that's it in terms of having your first build, there is a
 good idea to change security settings if you are not the only person on
 your network.
Good luck!

