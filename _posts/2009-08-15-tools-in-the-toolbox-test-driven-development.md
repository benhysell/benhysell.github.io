---
id: 260
title: 'Tools in the Toolbox &#8211; Test Driven Development'
date: 2009-08-15T11:32:25+00:00
author: benjamin hysell
layout: post
guid: http://benjaminhysell.com/?p=260
permalink: /archive/2009/08/tools-in-the-toolbox-test-driven-development/
categories:
  - tdd
  - Tools in the Toolbox
tags:
  - tdd
  - Tools in the Toolbox
---
<em>Tools in the Toolbox is a series of posts on development and design tools I use on a daily basis.</em>

I am a huge fan of Test Driven Development (TDD).  See my series I wrote for the Viewpoint Systems eNewsletter centering around TDD and LabVIEW:

Part 1

<a href="http://www.viewpointusa.com/newsletter/2008_november/newsletter_2008_nov2.php">http://www.viewpointusa.com/newsletter/2008_november/newsletter_2008_nov2.php</a>

Part 2

<a href="http://www.viewpointusa.com/newsletter/2009_january/newsletter_2009_jan2.php">http://www.viewpointusa.com/newsletter/2009_january/newsletter_2009_jan2.php</a>

Part 3

<a href="http://www.viewpointusa.com/newsletter/2009_february/newsletter_2009_febtdd.php">http://www.viewpointusa.com/newsletter/2009_february/newsletter_2009_febtdd.php</a>

Part 4

<a href="http://www.viewpointusa.com/newsletter/2009%20March/newsletter_2009_Mar.php">http://www.viewpointusa.com/newsletter/2009%20March/newsletter_2009_Mar.php</a>

Although I’ve written the most about my LabVIEW TDD experiences I use TDD in all of my development endeavors, be they text based in the .NET frameworks or web based development with JavaScript.  Now a days almost every modern language has a framework available to facilitate test generation and automatic test execution.

One of the first things I do when I start with a new language is to find the TDD framework and take the language and the framework for a spin.  No longer do I bother to create a ‘Hello World!’ app, I dive right into writing tests.  My last two language adventures involving Objective-C and Ruby on Rails attest to this fact.

However, as I sit here and write this post I already hear the groans emanating from a lot of developers,

::Put on your best Sean Connary voice from Celebrity Jeopardy on SNL::

<em>You see here Benjamin, I’ve tried a lot of development processes in the past, paid a pretty penny for them, I don’t mind telling you, for books and classes and whatnot.  None of these processes ever work.  So, I’ve got to ask you, will TDD really make my code better?  Does it really work man?</em>

As long as there no follow up questions, then yes, yes it does work.

Post over.

Oh, you are still here?

::Damn, now I have to come up with a few explanations...::

Why have I embraced TDD and given so much virtual ink to it?

Fear.

Out and out fear.

Back-story:

When I started with Viewpoint out of college I had endured my share of ‘larger’ software projects.  I say ‘larger’ because when you are snot nosed kid in college taking a semester to build a compiler it feels like a large project.  The late nights, the team of people toiling away on the code, and all of the social events missed as we attempted to get the compiler code to compile...we felt it was a large investment of time and resources.

I learned a lot on that project, but as with all college projects, once the semester was over I would never have to revisit the code base again.

Let’s face it, when you are on a co-op, one is only engaged with a company for maybe a three month period; there is rarely enough time to finish even a small project, let alone start a new one and then go back and make changes to your original project.  Thus, the idea that one might have to support and modify their code at a later point in time was a foreign concept to me leaving college.

Naturally, things quickly changed once I entered the real world.

I’ll put you in my shoes as a young developer: There you are, its 3:30 PM on a Friday.  Your software is crashing for some unknown reason, and you desperately want to be out of the office because it is one of the last weeks of summer.  Nothing has changed in the code.  Sure, you made a minor modification this morning to about a dozen code files, but the software was up most of the day, and now all of the sudden something is very wrong.  Your software is stopping and starting every couple of minutes and you can’t leave until it’s fixed.  You can’t just drop the changes you made this morning because those changes had fixed an even larger problem in the code, and by the way, do you remember where you made all of those changes?  You wrote them down, right?  You are using source control so you could roll back everything you just did, correct?

Picture yourself in a cube with flop sweat all over you realizing this isn’t how you want to run your career for the next 50 years, living in fear of every little minor change you make to a code base.

This is the realization I quickly came to soon after I started supporting some of my own code.  Forget supporting other’s code, this was my own code, I wrote it and it was still acting up on me.  I vowed that day to never live in fear again of modifying my code and not knowing what the results would be.

Fear motivated me to seek out a methodology that would help me avoid these types of situations.  I wanted to know when I was making a code change that change didn’t have an unforeseen ripple effect through the rest of the code base.  For me, TDD provided an avenue to help me avoid these situations by forcing me to generate a full suite of regression tests that I could quickly and easily run against any changes I made and validate I didn’t create any new bugs while fixing the code.

TDD isn’t perfect, and it isn’t free.  Can you make sure all of your test cases exhaust all possible inputs into your system?  What about the time spent developing the regression test suite?  How about the time spent maintaining and fixing the test when you make changes to how your code works?  All of these items have a cost associated with them.

It is near impossible to put a monetary value on the time and money one can save from using TDD.  How do you quantify,

<em>“Because I had regression tests I could run on my code the change I was going to make this morning never made it into the system because it broke all of the tests.  I re-architected the solution, ran the tests, and the new solution hasn’t caused us four hours of downtime.”</em>

How can one say their design methodology prevented a massive loss of time later down the road when you never experience that loss?  How can you really prove you can save time and energy with TDD?

In my travels so far I haven’t found a convincing argument that the time you spend writing and maintaining test cases saves time and money.  Basically, I have yet to acquire a DeLorean...sure I have the flux capacitor, but I don’t have the car to put it in, drive <em>Back to the Future</em>, and prove that if I didn’t have a regression test suite I would be in a world of hurt six months down the road while attempting to make a minor change.

Sadly, I believe one has to develop flop sweat a couple of times in their career before they will actively seek out a methodology like TDD and start applying it to all of their development endeavors, regardless of the inability to show a future cost savings.