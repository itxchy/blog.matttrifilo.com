---
layout: post
title: Leveling Up
comments: true
date: 2016-08-02T10:20:00Z
---

At the end of May, I finally completed my [Front End Certification](https://www.freecodecamp.com/itxchy/front-end-certification) for Free Code Camp.

Since then, I've spent some time refactoring a few of my older projects to get them to a place where I don't cringe every time I look at the source code. They were pretty grisly. My Pomodoro app from last year in particular was a hacky mess. It was my first project trying out AngularJS, and while it worked, it broke pretty much every relavent style in [John Papa's style guide](https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md). Here's the [old source](http://codepen.io/dprsky/pen/OymXEp) if you have a strong stomach. All of the logic was in one controller and $scope was polluted with almost every variable in the project. Not even one service to handle business logic. The controller was in charge or user interactions, business logic, *and* state. The horror. It must be destroyed. I didn't even know how to use a service versus a controller at the time. It's a small app, and the design-pattern-less layout of the code was well commented, but it would have quickly turned into a field of thorns if it ever needed new features. Good code should be mostly self-documenting anyway. I took this as an oppurtunity to refactor the app's code [structure](https://johnpapa.net/angular-app-structuring-guidelines/) and logic to separate concerns (UI elements, [state](https://johnpapa.net/sharing-data-in-an-angular-controller-or-an-angular-service/), application logic, user interaction handling) and get some practice using Angular 1.5's great new [component method](https://toddmotto.com/exploring-the-angular-1-5-component-method/), modeled after Angular 2's component pattern. That's a story for another blog post, but it was a humbling learning experience, and Angular 1's paradigm makes so much more sense now on every level.

The new pomodoro source code is [here](https://github.com/itxchy/FCC-pomodoro).

It's far from perfect, but application state is maintained in a single shared service (not spread out all over the application), the controllers simply handle user interactions, and the application structure is organized by feature.

Using [Redux](https://www.npmjs.com/package/ng-redux) would greatly [impove the state handling](http://blog.rangle.io/managing-state-redux-angular/), but that's for another day.

<!--break-->

Quincy Larson advises students not to bother refactoring old projects and just keep moving forward as you'll write better code as you go. He's absolutely right. I ended up shaving a [herd of yaks](http://www.catb.org/~esr/jargon/html/Y/yak-shaving.html) and spending hours on code I won't need for my portfolio once I build a few full stack applications.

Stubborn yaks aside, I'm glad I took the time to revisit my old projects. I learned a great deal about unit testing, was able to set up three very handy re-usable boiler plates, powered by Gulp and Browsersync, to build basic sites with Sass, modular sites with Webpack for Angular 1.x and React, and modular sites with an Express backend. Getting into the guts of Gulp, Bower, Webpack, and ES2015 was definitely worth the time and frustration. At least for now, I have the build systems I need to build my next generation of apps, and I don't have to worry about relying on "magic" from some of the complex boilerplates out there. There are some great ones that are well documented, but they should be used with caution and after a lot of tire-kicking. With my own, I can make a new gulp task or drop in a new Webpack plugin knowing exactly where and how to wire everything together without hours of research.

The biggest mistake I've made with boilerplates in the past was using some that used multiple technologies or syntax that I've never used before. You can learn one unknown fairly quickly, but if the boilerplate has two or three or six unknowns baked in to the stack, you're in for a world of hurt very quickly.

Thankfully, there are a lot of great boilerplates out there, each maintained by dozens of very smart developers representing many different opinions and stacks. Using a well-designed boilerplate can save a lot of set up time, as long as you understand every piece of the stack and can reason through the maintainers' code easily. The boilerplates worth using will be actively maintained by many smart people, be focused on best practices over individual opinions, and be very well documented in and out of the source code.

For React, a great site for shopping for boilerplates based on features is [here](http://andrewhfarmer.com/starter-project/).

One boilerplate that stands out to me is [React Redux Universal Hot Example](https://github.com/erikras/react-redux-universal-hot-example).

I haven't used it, but its stack uses very familier tools, nothing exotic. It's well documented--even the [demo app](https://react-redux.herokuapp.com/) has [a guide](https://github.com/erikras/react-redux-universal-hot-example/blob/master/docs/ExploringTheDemoApp/ExploringTheDemoApp.md), and the source is heavily commented. It also has over 100 contributers and close to 1000 commits. When I start working on some server-side rendered React apps for Free Code Camp's full stack projects, this will likely be my starter.

Over the next few months, I will be finishing a redesign for a friend's business site, working through Free Code Camp's back end challenges with Express, and learning all I can about NodeJS, Express, ReactJS, and Redux. The rest of the summer should be very exciting.

