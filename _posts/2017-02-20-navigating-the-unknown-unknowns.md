---
layout: post
title: Navigating the Unknown Unknowns
comments: true
---

## Navigating the Unknown Unknowns

As new coders traverse the vast, treacherous landscape of modern JavaScript developement in 2017, many chasms in knowledge appear out of the fog that can stop them in their tracks. There's no avoiding it. There's no guessing where they might even arise at times.

In my case, the first "knowledge chasm" of many appeared after completing Codecademy's JavaScript course, and finishing all of Free Code Camp's beginner algorthim challenges. I felt pretty confident. JavaScript was becoming familiar, and I built web pages with HTML, CSS, and jQuery with increasing ease.

Then came the Free Code Camp Intermediate Front End Development challenges to build simple client-side apps like a quote generator and a weather app. I barely knew where or how to begin. The guided trail ended and the time came to venture into wild. Blocking the way I wanted to go was a gaping, vertical ravine. How do you cross it? The simple answer is to find the right tools you need to repel down and then climb up the other side. Learn which tools to use when, where, and why, learn them well, learn how to use them together, and you can reach your goal. Easy right? Not so much.

<!--break-->

That process is much easier said than done. When it comes to software, the reality is that new chasms are always just ahead of you. If you can learn how to *approach* the unknowns correctly, you can build just about anything. Not easily (not at all!), but that's the game. Over time, it will become more and more intellectually and personally fulfilling as you solve bigger and harder problems, build better software, and start to make a tangible impact in the lives of real people. If you're still new to programming, you won't be building software in a vacuum forever.

Thankfully for software developers today, many of the most powerful tools out there are free and open source to solve a multitude of common problems, and they're improving all the time. Learning to fully understand the problems in front of you and find the most viable solutions to them through code or libraries is a journey that takes a lifetime to master, and best practices in software are moving targets. Quality software is transient, which is great because as technologies improve, so do the possiblities of what we can build. Our goal should be to help the landscape change for the better wherever we can so we can use these tools and ideas to build quality, stable, secure software as a means for improving the lives of real people and saving their precious, limited time on this planet.

How do you approach the menacing unknowns in practice? For a simplified example, let's go back to my first "knowledge chasm" because "climbing down and up a ravine" is a bit of a cop out.

## Approaching the Big Problem

Putting all of the technologies I've learned up to that point together to build something like a [calculator](https://calculator.matttrifilo.com/) seemed almost impossible at first glance. Here's how I approached the "ravine."

First, I thought about the what made up the Big Problem (making a calculator app) and broke it apart. A calculator app will need an HTML page with a UI that has a "screen" div, number buttons, and operator buttons, all with id's that JavaScript can hook on to. The UI will get styled with fancy CSS. Then, JavaScript logic will handle the button clicks and display updates.

Digging into the JavaScript logic, I had to then break the problem apart further and further. What happens when a number is clicked? A plus sign? The Equal sign? The '.'? How will the display screen div update the numbers and when? As the big problem broke apart into smaller and smaller problems, they became easier to solve individually adding up to the whole.

Number buttons had a click handler to update the display, and each operator button triggered an operator click handler that would put the displayed numbers into the clicked-operator's array, clear the display on the next number press, and put the new numbers into the next index of that same array, and apply the calculation to both numbers in the active array as soon as the equal sign, or another operator button, gets pressed, and finally update the display with the new total. Thinking about all of that logic at the same time is too much for the brain to handle for long, but thinking about each piece individually and putting them together synchronously, step by step, makes code much easier to reason about and extend as nessessary. Here's the same logic broken up:

* A number button click: triggers the number click handler
* number click handler: updates the number display
* operator button click: triggers the operator click handler
* operator click handler:
  - stores the displayed number in index 0 of the  clicked operator's array
  - clears the displayed number variable (updates UI on next number press)
* A number button click: triggers the number click handler and updates the number display
* An operator button click: triggers the operator click handler
* operator click handler:
  - pushes the displayed number into index 1 of the previous operator's array
  - calculates the two numbers based on the previous operator
  - then puts the new, calculated number into index 0 of the currently pressed operator array
  - clears the previous operators array
  - updates the display with the new result immediatly
  - clears the displayed number variable (updates UI on next number press)

This is similar to psuedocode, though there is repetition to make it clear what the code should be *doing*. Looking at this list, you can easily implement this logic piece by piece. Not all edge cases are described here as its very generalized for brevity, but the edge cases are mostly straight forward to account for once the core functionality is defined.

The calculator is a simple example, but this method of breaking up large problems into managable pieces and reasoning through them can scale up to building very large, complex applications. At that level, tools and frameworks are going to become essential for working with teams, ensuring compatibility with all major browsers or systems, and not reinventing the wheel at every turn.

## Chosing the Right Tools

Using module patterns and helpful libraries and frameworks can abstract away many common implementation details and allow different layers of the stack you're using to work well together, whether you're building a universal web app, a game, or an Electron desktop app. Servers, databases, and client-side code are easier to wire up together than ever before. We're thankful to live in an era in 2017 where there are many great frameworks and libraries that are built to solve all sorts of common problems, but applying them to the software we're trying to build still takes that same degree of problem solving to know what to use and when, why, and how. Hand-holding tutorials will only get you so far. The devil is in the details, and you'll certainly encounter edge cases you didn't anticipate that will take a lot of research and understanding to solve.

In the React ecosystem for example, it's important to consider when to store your application state in Redux vs letting your React components handle their own state. Each approach is prefereable in certain situations, but it's important to understand when to bring in Redux. If your app's components don't likely ever need to share state, then Redux is probably overkill. It will make development more tedious, and increase the code surface area for bugs. On the other hand, if your apps componets need to share a lot of state up and down the component tree, you'll run into "data-tunneling" problems and have a very hard time refactoring. In this situation, the extra effort required to use Redux is well worth it since your components will be far more portable, refactoring will be easier, and changing redux logic won't affect the components who receive the Redux state as props.

Google, official documentation, Stack Overflow, Slack, Gitter, Discourse forums, FreeNode, and ideally your mentor can answer just about anything you ask about your problem if its a common one. Learning to ask the right questions is key.

Over time, this process of breaking up problems and deciding when and how to use libraries and frameworks becomes easier and patterns start to emerge. There are many front end frameworks out there like React, Angular, Ember, and Vue, but they are all providing varying code structures and approaches to solving the same sort of problems. All of them are meant for building dynamic user interfaces that handle user interactions and data flow to put it very simply. The APIs, code structures, and scope of responsibilties vary, but end goal is the same.

Whether you use vanilla javascript, or a modern stack of libraries, there are many ways to cross the ravine.
