---
id: 339
title: 'Tools in the Toolbox &#8211; Source Control'
date: 2009-10-31T16:01:54+00:00
author: benjamin hysell
layout: post
guid: http://benjaminhysell.com/?p=339
permalink: /archive/2009/10/tools-in-the-toolbox-source-control/
categories:
  - coding
  - Tools in the Toolbox
tags:
  - source control
  - svn
  - team foundation server
  - Tools in the Toolbox
---
<em>“Tools in the Toolbox” is a series of posts on development and design tools I use on a daily basis.</em>

You use source control, right?

Good, I’m not going into the benefits of using source control, it should be a no brainer in this day in age.

No, you don’t use a system to back up every revision you make to your code?

Ok, here’s what you are going to do.
<ol>
	<li>Finish reading this blog post and then never come back to my blog again.</li>
	<li>Close your browser</li>
	<li>Uninstall all of your development tools and never write another line of code.</li>
</ol>
I’m not kidding.  If you are not using some form of source control for your daily coding activites I’m not sure we can be friends any more.  I mean, before I learned this about you I thought of us as at least friendly, but this is a deal breaker.

All snarkiness aside, source control is one of those fundamental tools like a hammer or screwdriver that needs to be in your toolbox.  Having a system that allows one to go back in time and see every change that has ever been made to a piece of code is invaluable.

Are you telling me you wouldn’t want to time travel back in time given a chance?  I’m offering you that opportunity to do so without the need of a DeLorean or a Flux capacitor.  You would turn down time travel?  Sir, I must say good day.

Won’t stop reading eh?  Maybe there is hope for you yet...let’s look at two scenerios, one with source control and one without.

<em>It is 10pm, you should have been home hours ago.  Diner is cold, wife is mad.  You are in the office, your hard drive died at 4 this afternoon.  You have lost all of your code for your presentation you are going to give tomorrow, and you are only half way done recreating all of that code.  You have a long night ahead of you.</em><em></em>

Try that same situation, but this time you and I are still friends because you use source control.

<em>It is 5 pm and you are sitting down for a nice meal with the family.  You had a rough day in the office, your hard drive died right around 4 pm right after you put the finishing touches on your presentation you are giving tomorrow.  Since you use source control you have copies of all of your code in a repository on a seperate server.  You contact your IT guy, grab a spare laptop, connect to the source control server and pull down all of the code you have been working on.  You check it over, its all there.  A minor emergency before your presentation, but luckily you have avoided a full night of rewriting all of your code, and can go home and relax.  Thank goodness for a good source control system</em><em></em>

Contrived?  Yes, but still proving the point of having copies of your work in a system off of your main development computer will avoid disaster.  Sure, you could have avoided this scenerio if you just stored a copy of your code on a thumb drive or a network drive, but then you would lose all of the benefits a source control system brings to your development environment, each done with only a few clicks:
<ul>
	<li>One click check in process – Most systems now a days integrate directly into your IDE.  With a few clicks a copy of your code is placed into the system.  Total time, five seconds, max.</li>
	<li>One click get the latest copy – When working on a team often times people make changes you’ll have to get a copy of.  Instead of bothering them you can normally right click on your project and select ‘Get latest’ to pull down the latest copy of all of the code stored in the system.</li>
	<li>Compare – Allows you to take the copy of the code you are working on and compare it to the code that is sitting in the source control system for changes.  Did you accidentally erase a key section of code and then re-save your code?  Compare it and pull back that key piece of code.</li>
	<li>Merge – Are you working on the same piece of code your buddy is working on?  Who has chnaged what?  One click and the source control system can help you merge his changes with yours.</li>
	<li>Shelve – Are you working on a cool new feature only to have an emergancy bug crop up that needs addressing ASAP?  Take your code and put it on a shelf in the source control system.  Shelving code takes a snap shot of your current code and places it in a special area allowing you to go get a new copy of your code from the repository, fix the bug, check in the changes, and then go and get the code you were working on, apply the changes, and then continue on your merry way, saving time and energy.</li>
	<li>View older copies of your code – This is the “Back in Time” scenairo, you need to see changes made last month to a key algorithm, with a source control system you can go back and grab those changes since you are saving a copy of every change ever made this isn’t an issue.</li>
</ul>
I use source control for everything I write, code or otherwise.  Every document I write for this blog is backed up in a source control system.

For the sake of our friendship, please go and grab something, anything to put your code into.  I am a huge fan of Team Foundation Server from Microsoft, but SVN has been growing on me.  There is a huge list of systems out there, pros and cons, if you Google for them.

Take a hour, download a source control system, set it up, and move all of your code into it.  You might not thank me today, or tomorrow, or the next, or even late next year.  Some day, some day in the future, you are going to rely on that source control system and you will be thankful you have it.