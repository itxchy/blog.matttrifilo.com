
---
layout: post
title: Beating JavaScript Fatigue
comments: true
---

JavaScript fatigue was a big topic of conversation last year, but one thing I realized is if you just take the time to really learn and seek to understand the tools you're using, the pain points fade quickly. New technologies move forward all the time, but they really don't take much time to get comfortable with if you take a few days to go through the documentation, code along with a tutorial or two, or even read a book on the topic. A lot of great books are freely available online about most tools out there. Use what makes you the most productive. Don't let the opinions of others slow you down.

A lot of the competing tools and frameworks are seeking to solve the same problems. 

Let's look at build tools.

Grunt, Gulp, and Broccoli are all build tools that offer different approaches to automating common tasks. You can accomplish the same goals using NPM scripts. It all comes down to which approach you agree with the most, not what is "right". No one has the authority to say what is "right" or what is "best" in programming. Each tool has advantages and shortcomings (some more than others), but at the core, they all seek to accomplish the same goals. Even if one tool accomplishes an edge case better than the other, you're better off using the latter if you know it inside out and can navigate any shortcoming, rather than learning an entirely new tool just for a small edge case. Grunt may not be the hot new thing on Reddit anymore, but that doesn't make it any less powerful and useful. Many developers working on large, complex projects found themselves with large Gruntfiles that were overly complicated and sluggish, so they found Gulp which uses NodeJS streams and runs tasks asynchronously. Then, many other developers fed up with all of the configuration proclaimed that Gulp and Grunt are not necessary, and writing build NPM scripts for build processes is the best solution for them.

That's not to say you shouldn't learn a new technology when it solves a clear problem you're facing, but use what works best for you. If you find a pain point that a different build tool solves, explore it! If the build tool you're experienced in and comfortable with is working great for you, your projects, and your team, don't just switch to a new one just because something new is trendy. It may solve some of the problems you've been facing, but that must be weighted against the time it would take to understand and implement the new tool with your project(s), and how stable it is, how much documentation surrounds it, what plug-ins are available and stable, and on and on. Gulp, Grunt, and NPM are all very stable and documented at this point, but new tools and trends will always emerge every year in this line of work.

The same applies to the major JavaScript frameworks out there. It's my opinion that ReactJS, Angular 1/2, and Ember should be the ones to focus on right now since they each have been heavily battle tested in large production applications, are being maintained by phenomenal teams of developers and companies, and will likely be around for years to come. 

Which one should you learn?

Over the past year especially, that question has become less relevant. In years past, many frameworks have had very different approaches (again) to the same problem that plagued jQuery and PHP developers. How do you organize the code of a complex application in a way that's scalable, allows new developers to dive in and reason through the code easily and be productive quickly, ensure insecure or non-performant code patterns are prevented, and allow easy testablity? 

A lot of approached have been tried with varying success, but the largest frameworks have finally converged with a similar component-based approached to building applications--and that's great news.

React, Angular 2, and Ember have not only embraced components, but they also have CLI build tools, great routers, in-depth developer tools, verbose error messages that hint at solutions, and most importantly, large communities that are sharing there knowledge, tools, and help with everyone for free.

Each has its strengths and weaknesses in many regards, but any developer can now reasonably learn one very well, and switch to another at a new job without too much of a culture shock compared to switching from Backbone to React in years past.

This is a very exciting time to be a developer, and its only going to get better over time. 
