---
id: 347
title: 'Tools in the Toolbox &#8211; Personas'
date: 2009-11-05T08:24:35+00:00
author: benjamin hysell
layout: post
guid: http://benjaminhysell.com/?p=347
permalink: /archive/2009/11/tools-in-the-toolbox-personas/
categories:
  - Alan Cooper
  - persona
  - Tools in the Toolbox
tags:
  - personas
  - Tools in the Toolbox
---
<em>“Tools in the Toolbox” is a series of posts on development and design tools I use on a daily basis.</em>

<em> </em>

I am a huge fan of Alan Cooper and personas…huge fan.  I have written at length about him and his techniques here, <a href="http://benjaminhysell.com/archive/2009/02/alan-cooper/" target="_blank">http://benjaminhysell.com/archive/2009/02/alan-cooper/</a>.  My goal in this article is not to cover the basics; other blogs and books cover the basics of personas.  Rather, I hope to cover why I use personas, and why I have embraced them.

So, why do I love talking about personas and using them for quoting, specifications, and development work?

Fear.

::No wait, I’ve used ‘Fear’ already as a motivator for using Test Driven Development here: <a href="http://benjaminhysell.com/archive/2009/08/tools-in-the-toolbox-test-driven-development/" target="_blank">http://benjaminhysell.com/archive/2009/08/tools-in-the-toolbox-test-driven-development/</a>.   I can’t just keep throwing around the same emotions...I had better come up with something different...::

Take two.

One reason.

I hate designing software for the person named “user”.

Think back to the last meeting you were in with your developers.

<em>I have a great new idea for your blog website!  I think some of the users who use your website get lost in the technical jargon you use.  Let’s add a whole section that explains all of the technical terms that appear in your blog entries on the right hand side of each page.  That way when people come up against a new word they don’t know they can just glance to the right side and see a definition!</em>

<em> </em>

This actually isn’t a bad idea on the surface.  When my non-technical friends read my blog they may walk away with something more than just saying, “Wow, you write a lot of words!”

Then another developer chimes in…

<em>Let’s build an iPhone app that links back to this blog!  Every time you publish a new article it’s automatically sent to the user’s iPhone, they get a notification that a new article is up and their phone will “ding” with a new article notification!</em>

Again, not a bad idea…technically possible, would be kind of fun to implement, figure out the whole push notification framework.  Yes, that is a project I could lose myself in for a while, and in the end it would be sort of neat to have figured out all of that technology and to get something working.

Here is the rub, although each idea is fun, interesting, and very technically possible, neither one does anything for the core function of my blog.  Neither idea helps me write about managing people, software, and projects.  Nor is either idea squarely aimed at my core audience, other developers, project leads, and managers.

By using personas I can quickly and easily figure out if either idea is worth while exploring and implementing.

How you may ask?  Simple, I can quickly take every idea I have for my blog and ask myself,

<em>If I had an iPhone app that notified John that I had published a new article would he use it?  Would it be worth the effort to publish such an app, and would John tell his friends about it?  Would John pay for an app like this?</em>

Who is this John?  Good question…

Before going any further let me introduce you to John.

Name
<ul>
	<li>John</li>
</ul>
Age
<ul>
	<li>early 30s</li>
</ul>
Personality
<ul>
	<li>Enjoys going out with his friends for weekly “Man Night” where many wings and beers are consumed</li>
	<li>Plays sports year round, golf in the summer, hockey in the winter</li>
	<li>Stays up to date on the latest technologies by reading whatever he can get his hands on</li>
</ul>
Skills
<ul>
	<li>He is his families IT go to guy.</li>
	<li>Loves learning new programming languages for the challenge of it</li>
	<li>Enjoys manual labor after a long hard day of pushing pixels all over the computer screen.</li>
</ul>
Goals
<ul>
	<li>Desire to own his own company some day</li>
	<li>Realizes there is more to staying competitive in the market place than just knowing the latest software frameworks and architectures, hence he is reading outside of his field to learn how to do marketing and sales.</li>
	<li>Aspires to have his own technical blog</li>
</ul>
Attitudes
<ul>
	<li>Loves all forms of technology</li>
	<li>Willing to be the first one to try new operating systems, development techniques, and be on the bleeding edge of all new technology.</li>
	<li>Has a hard time stepping back from the computer at night since there is so much to continually learn.</li>
</ul>
John seems like a pretty good guy.  The sad part is he doesn’t exist except for me to test my blog topics, design decisions, and possible iPhone apps against to see if he would like them.

John is my sounding board.  John replaces all instances where I might go, “Would the user like this?”  Now all I ask is “Would John find this useful?  If I spend time on this would John tell his friends to check this out?”

Designing my blog towards a someone, a real defined someone, helps me figure out what I should do and keeps me focused on my core audience.  Remember that iPhone app?  John wouldn’t need it, John would be using RSS to keep up to date with my blog.

What about the jargon pane on the right hand side of the site?  John probably knows the jargon already, and if he doesn’t, i.e. its something new to him, he is going to be motivated enough to go look it up himself using Google.

I love writing for my wife,

::Thanks for proofing this for me!::

but if she comes across a term she doesn’t know she will likely use the surrounding paragraphs to figure it out or skip it all together.  I don’t care if she doesn’t get the whole article because she isn’t my core audience.

John just saved me hours of time implementing ideas for a “user” who isn’t going to read my blog.

For every project I sit down and define who my end users are for that project before I start.  Each one gets a name, age, personality, skills, goals, and attitudes creating a vivid image of the person I hope will be using my software.  Every decision I make, be it user interface or back end operation I ask,

<em>Will John like this new feature?  Can he even use this new feature given his skills?  With his attitudes would he want to use this new feature?</em>

I instantly know which features I should implement and which ones should go back to the drawing board.  Personas have become my strongest tool for designing software that people actually want to use.

Next time you get in an argument about an implementation detail or presentation issue ask out loud, “will our main user Steve actually like what I am creating for him?”

When a developer asks to implement this crazy new login procedure ask them, “Will Sally, our 75 year old grandmother who is our main user be able to figure this out?”

Bring real people into your development and you won’t be disappointed with what they tell you about your software or new idea.