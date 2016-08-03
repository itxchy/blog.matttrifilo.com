---
layout: post
title: Leveling Up
---

Last month, I finally completed my Front End Certification from Free Code Camp. 

Cheers!

Since then, I've spent a lot more time then I anticipated refactoring some of my older projects to get to a place where I don't cringe every time I look at the source code. They were pretty grisly. My Pomodoro app from last year in particular was something I wouldn't have wanted anyone to look at under the hood. It was my first project trying out AngularJS, and while it worked, it broke just about every one of John Papa's best standards. All of the logic was in one controller and $scope was polluted with almost every variable in the project. It's a small app, but it was already hard to reason about, and would have quickly turned into a field of thorns if it ever needed new features. I took that as an oppurtunity to refactor the app into modular components using Angular 1.5's great new component sugar, modeled after Angular 2's component pattern. That's a story for another blog post, but it was a humbling learning experience, and Angular 1's paradigm makes so much more sense now on every level.

<!--break-->

Quincy Larson advises students not to bother refactoring old projects and just keep moving forward as you'll write better code as you go. He's absolutely right. I ended up shaving a herd of yaks and spending hours on code I won't need for my portfolio once I build a few full stack applications. 

Stubborn yaks aside, I'm glad I took the time to revisit my old projects. I learned a great deal about unit testing, was able to set up three very handy re-usable boiler plates, powered by gulp and Browsersync, to build basic sites with Sass, modular sites with Webpack for Angular 1.x and React, and modular sites with an Express backend. Getting into the guts of Gulp, Bower, Webpack, and ES2015 was definitely worth the time and frustration. At least for now, I have the build systems I need to build my next generation of apps, and I don't have to worry about relying on "magic" from some of the complex boiler plates out there. Anytime I need to make a new gulp task or drop in a new Webpack plugin, I know exactly where and how to wire everything together.

## Beating JavaScript Fatigue

JavaScript fatigue was a big topic of conversation last year, but one thing I realized is if you just take the time to really learn and seek to understand the tools you're using, the pain points fade quickly. New technologies move forward all the time, but they really don't take much time to get comfortable with if you take a few days to go through the documentation, code along with a tutorial or two, or even read a book on the topic. A lot of great books are freely available online about most tools out there. Use what makes you the most productive. Don't let the opinions of others slow you down.

A lot of the competing tools and frameworks are seeking to solve the same problems. 

Let's look at build tools.

Grunt, Gulp, and Broccoli are all build tools that offer different approaches to automating common tasks. You can accomplish the same goals using NPM scripts. It all comes down to which approach you agree with the most, not what is "right". No one has the authority to say what is "right" or what is "best" in programming. Each tool is better suited for certain specific cases, but at the core, they're all just tools powered by JavaScript. Even if one tool accomplishes an edge case better than the other, you're better off using the latter if you know it inside out and can navigate any shortcoming, rather than learning an entirely new tool just for a small edge case. Grunt may not be the hot new thing on Reddit anymore, but that doesn't make it any less powerful and useful. Many developers working on large complex projects found themselves with large grunt.json files that were overly complicated, so they found Gulp which uses NodeJS streams and functions which were much cleaner. Then, many other developers fed up with all of the configuration proclaimed that Gulp and Grunt are not necessary, and writing build NPM scripts for build processes is the best solution for them.

That's not to say you shouldn't learn a new technology when it solves a clear problem your facing, but use what works best for you. If you find a pain point that a different build tool solves, use it! If the build tool you're experienced in and comfortable with is working great for you and your projects, don't just switch to a new one just because you read a blog post.

The same applies to the major JavaScript frameworks out there. It's my opinion that ReactJS, Angular 1/2, and Ember should be the ones to choose from right now since they each have been heavily battle tested in large production apps, are being maintained by phenomenal teams of developers and companies, and will likely be around for years to come. 

Which one should you learn?

Over the past year especially, that question has become less relevant. In years past, many frameworks have had very different approaches (again) to the same problem that plagued jQuery and PHP developers. How do you organize the code of a complex application in a way that's scalable, allows new developers to dive in and reason through the code easily and be productive quickly, ensure insecure or non-performant code patterns are prevented, and allow easy testablity? 

A lot of approached have been tried with varying success, but the largest frameworks have finally converged with a similar component-based approached to building applications--and that's great news.

React, Angular 2, and Ember have not only embraced components, but they also have CLI build tools, great routers, in-depth developer tools, verbose error messages that hint at solutions, and most importantly, large communities that are sharing there knowledge, tools, and help with everyone for free.

Each has its strengths and weaknesses in many regards, but any developer can now reasonably learn one very well, and switch to another at a new job without too much of a culture shock compared to switching from Backbone to React in years past.

This is a very exciting time to be a developer, and its only going to get better over time. 
