---
id: 355
title: Breaking Down the Game Film – Micro Code Reviews
date: 2009-11-17T08:34:55+00:00
author: benjamin hysell
layout: post
guid: http://benjaminhysell.com/?p=355
permalink: /archive/2009/11/breaking-down-the-game-film-micro-code-reviews/
categories:
  - Breaking Down the Game Film
  - coding
  - management
  - team building
tags:
  - Breaking Down the Game Film
  - development
  - management
  - team building
---
<em>“Breaking Down the Game Film” is a term commonly used to analyze tape from an already played sports game to dissect what went right and what went wrong.  In this series I’ll be taking published articles from around the web and break them down.</em>

Topic: Micro Code Reviews

Article: “The Joy of Code Reviews”

Author: Tom Hollander

Link: <a href="http://blogs.msdn.com/tomholl/archive/2009/01/06/the-joy-of-code-reviews.aspx">http://blogs.msdn.com/tomholl/archive/2009/01/06/the-joy-of-code-reviews.aspx</a>

Short Summary:  Full code reviews are done too infrequently, and take too much time to complete to be done with any real regularity.  Micro code reviews, performed before every check-in, provide you and your project all the benefits of a full fledged code review, but also facilitate knowledge transfer between team members, an avenue to provide mentoring, and a chance for your team to “show off” to one another that cool new algorithm they have been working on.  All in 15 minutes or less!

Analysis:

When was the last time you reviewed the whole codebase for your project?  Stem to stern?  I know, I know, who has the time.  Everyday the codebase gets larger and larger, and each one of your team members becomes more and more specialized in whatever it is they are working on day in and day out.  What happens if one day George walks in and goes, “I’m off to Alaska to follow my one true calling.  Being a software developer is nice and all but I wanted to be… a lumberjack!”

::Deep down you know he’s a lumberjack, and he’s ok.  He likely will sleep all night and work all day, but I digress…::

George is gone.  When was the last time anyone was looking at his code?  Who on your team knows how his really cool, yet very complex, algorithm runs?  Sure it all works, but who last put eyes on his code to try to and understand it?

If I know your team, and I likely don’t, but I’ll assume I know them, no one has looked at his work in a while.  Why would they?  George’s code worked, all of his tests passed, who has the time to review working code?

Your project has just suffered a major brain drain, and you don’t have a plan on how to recover quickly.

Tom’s article, “The Joy of Code Reviews”, provides some great reasons for you and your team to adopt what I’ll call micro code reviews.  A micro code review works like this:
<ul>
	<li>Before every check-in to source control George grabs a fellow team member, Stan, and they review all the changes that are about to be committed to source control.</li>
	<li>In Tom’s article he states these reviews often last 15 minutes, some much shorter, some a little longer.  Fifteen minutes seems just about right for a couple of programmers to review a bug fix or feature enhancement and has been the typical amount of time my team and I have taken for our own micro code reviews.</li>
	<li>After George and Stan have reviewed the code, ensured the unit tests pass, and then check the code into source control.</li>
</ul>
Pretty simple and straight forward; spend 15 minutes before every check-in, or 30 minutes billed to your whole project, to get a second set of eyes on the code that is getting into your source tree.  On top of that:
<ul>
	<li>In his article Tom calls out one of the great benefits in doing these reviews is the mentoring that can take place between two developers.  George has a chance to show Stan the new functionality he has implemented and possibly a new way to write code, debug, or write tests.  As Stan is exposed to George’s style he may pick up a tip or trick just watching him work.</li>
	<li>I’ve personally seen George improve his ability to explain complex thoughts and algorithms to Stan.  Oftentimes we work in isolation, only explaining things to ourselves.  By sharing with Stan and forcing himself to verbally explain things George has a chance to work on his presentation skills as he breaks down a complex problem to Stan.</li>
	<li>Micro code reviews provide a chance for a team to feel more like a team as they work directly with one another.  I feel my team has gelled together a little more with these mini interactions that take place during these micro code reviews.</li>
	<li>One might think developers would be resistant to these micro code reviews, however I found when I argued one could “show off” that cool new thing they did everyone seemed to get on board after doing a few code reviews.  Software developers love to have the admiration of their peers, give them a stage and most of them will step up and gladly perform.</li>
</ul>
I’ve adopted micro code reviews for my team and have had some excellent results.  Team members are grabbing one another when before new code is committed to the system.  Spirited debate takes place between the developers while reviewing the code, and learning is going on as each one learns from one another.  For us it has been a huge win win.

So when George answers his primal calling to be a lumberjack…

:: <em>He cuts down trees. </em>

<em>He eats his lunch. </em>

<em>He goes to the lavatory.</em>

<em>On Wednesdays he goes shopping</em>

<em>And has buttered scones for tea.</em>

I’m done, I’m done, I swear.  No more Monty Python for this post...::

You and your project will be prepared.