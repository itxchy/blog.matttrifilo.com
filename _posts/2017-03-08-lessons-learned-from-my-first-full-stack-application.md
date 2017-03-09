---
layout: post
title: Lessons Learned from My First Full Stack Application
comments: true
date: 2017-03-08T10:40:00Z
---

All of the things I've been learning for the past two years were finally brought together to build out my first full stack application for Free Code Camp, a polling app. It's [deployed](https://fcc-vote.matttrifilo.com)! You can view the source on [GitHub](https://github.com/itxchy/FCC-vote).

I took my time on this project because while I was familiar with React, Express, and MongoDB at a basic level, I'd never used them together to build a large, modern CRUD application. Piling on to that, I had no experience at all with Redux or the Flux pattern in general, much less how to pass state around in a larger React/Redux application. This was also my first experience visualizing data with D3.js and dealing with user authentication.

I've learned more from this project than anything else I've ever built and this seems like a good time to take a step back to reflect on and share some of the most useful lessons I've learned from this project.

<!--break-->

This is a very long post, so feel free to jump around.

<!-- MarkdownTOC -->

- [Thank You Virtual Mentors](#thank-you-virtual-mentors)
- [An Overview of Vote](#an-overview-of-vote)
- [Tooling](#tooling)
  - [Linting with Standard JS \(Use with caution\)](#linting-with-standard-js-use-with-caution)
    - [Why Standard \(or Semistandard\)?](#why-standard-or-semistandard)
    - [Using Standard Without React](#using-standard-without-react)
    - [Using Standard With React](#using-standard-with-react)
  - [Webpack 2](#webpack-2)
    - [Optimizing Your Bundle\(s\)](#optimizing-your-bundles)
      - [Analyzing a Bundle with `webpack-bundle-analyzer`](#analyzing-a-bundle-with-webpack-bundle-analyzer)
      - [Splitting Bundles](#splitting-bundles)
      - [Optimizing Bundles for Production](#optimizing-bundles-for-production)
    - [Tree-Shaking](#tree-shaking)
    - [Linting with Webpack](#linting-with-webpack)
      - [One Caveat](#one-caveat)
- [The Client](#the-client)
  - [React](#react)
    - [`React.createClass` vs ES6 classes](#reactcreateclass-vs-es6-classes)
    - [PropTypes](#proptypes)
    - [Component Hierarchy](#component-hierarchy)
      - [Spiffy Wikipedia Example](#spiffy-wikipedia-example)
  - [Redux](#redux)
    - [When Do You Need Redux?](#when-do-you-need-redux)
    - [A Ducks Pattern Variant](#a-ducks-pattern-variant)
    - [Thunks Middleware](#thunks-middleware)
  - [Unit Testing](#unit-testing)
    - [Testing React with Jest and Enzyme](#testing-react-with-jest-and-enzyme)
    - [Testing Redux with Jest](#testing-redux-with-jest)
  - [Styling](#styling)
- [The Server](#the-server)
  - [Server Side Rendering](#server-side-rendering)
      - [`React.createElement` Refresher](#reactcreateelement-refresher)
    - [Pre-Transpiling For Node Using Babel](#pre-transpiling-for-node-using-babel)
  - [Express API Routes](#express-api-routes)
    - [RESTful Dialog Between Machines](#restful-dialog-between-machines)
    - [CRUD Logic](#crud-logic)
    - [Avoiding Callback Hell With Async/Await](#avoiding-callback-hell-with-asyncawait)
      - [Not So Fast...](#not-so-fast)
- [The Database: MongoDB](#the-database-mongodb)
  - [Schema](#schema)
  - [Mongoose](#mongoose)
  - [Development Database vs Production Database](#development-database-vs-production-database)
- [Production](#production)
  - [Continuous Integration](#continuous-integration)
    - [Staging vs Production](#staging-vs-production)
    - [Logging](#logging)
- [Security](#security)
  - [Watch this talk](#watch-this-talk)
  - [Securing Express](#securing-express)
  - [HTTPS](#https)
- [This All Comes Back to JavaScript](#this-all-comes-back-to-javascript)

<!-- /MarkdownTOC -->


# Thank You Virtual Mentors

First off, I'd like to thank [Brian Holt](https://twitter.com/holtbt) for both of his mind-bending Complete Intro To React workshops on [Front End Masters](https://frontendmasters.com/courses/?u=563312949abcfdd369685f8abe03f2bec24ead42). The workflow he shares is a simple and scalable way to think about building a React application. As you build it up, you bring in new tools only as you need them, and refactor fast and often. I can't recommend his workshops enough, even if you've been using React for years. The insights he shares are well worth the Front End Masters subscription. Check out all of Kyle Simpson's workshops while you're at it!

I'd also like to thank [Rem Zolotykh](https://twitter.com/remzolotykh?lang=en) for putting together such a thorough [Youtube series](https://www.youtube.com/playlist?list=PLuNEz8XtB51K-x3bwCC9uNM_cxXaiCcRY) about building a React/Redux application with jwt authentication. While you should never use a custom authentication strategy for real-world production apps (use [Passport](http://passportjs.org/) instead), he offered a detailed overview of how to build an authentication flow using JSON web tokens, localStorage, and the proper headers. It was also very helpful to get another perspective of how to wire up React and Redux, and handle forms with controlled components.

Finally, I owe a heavy amount of gratitude to Robert M. Pirsig. While working on this project, I hit a lot of brick walls. To unwind after getting put in my place by a waterfall of error messages, I would read [Zen and the Art of Motorcycle Maintenance](https://www.amazon.com/Zen-Art-Motorcycle-Maintenance-Inquiry/dp/0060589469). After picking at the first half for about a year, I finished the second half within a few weeks while working on this project. The explorations of Quality in the book correlate directly with programming, and with life in general. It put a lot of mental blocks I had with thinking about code into a much broader perspective, and inspired a deeper sense of kinship with the craft. It changed the way I perceive and approach bugs. I've gained an appreciation for them, as frustrating and ego-crushing as they can be. Each hard bug illuminates a gap in knowledge with an opportunity to learn something profound, and you grow because of it. As Ryan Holiday's cult-classic is titled, "[The Obstacle is the Way](https://www.amazon.com/Obstacle-Way-Timeless-Turning-Triumph/dp/1591846358)." In turn, this leads to writing better *quality* software naturally and becoming better prepared and energized to contribute fresh perspectives and innovations to open source projects, companies you're working for, or your even an entire industry by finding your own [blue ocean](https://www.amazon.com/Blue-Ocean-Strategy-Expanded-Uncontested/dp/1625274491/ref=sr_1_2?ie=UTF8&qid=1417408285&sr=8-2&keywords=blue+ocean+strategy) with your own open source project or company. Quality wins out in the end. Customers know it when they find it. A bug may seem trivial at first, but if you consider that an entire application may rely on that trivial bug being fixed in order to run the way it needs to, it's not so trivial after all, and deserves attention and careful thought.

There seem to be a lot of nods to Stoicism in Zen and the Art of Motorcycle Maintenance, and I'm looking forward to to re-reading it after I learn a bit more about the Stoics. "The Obstacle Is the Way" at on the top of my list.

# An Overview of Vote

The user stories for this Free Code Camp project are [here](https://www.freecodecamp.com/challenges/build-a-voting-app).

Vote is a server-side rendered single page application that allows you to create and manage polls as an authenticated user, vote once on any poll whether you're authenticated or not, view poll results instantly after voting, and share a single poll with friends and strangers.

It uses React and Redux for the client side application, and Express and MongoDB for the back end API and database. For each initial request, `ReactDOMServer` renders the entire React application's markup into a string, which gets injected into `index.html` before being sent to the client. Authentication is handled with JSON web tokens.

This project is far from perfect. You can't search or sort polls, but this app satisfies all of the user stories for Free Code Camp, and that was the intent. Vote won't be found on "[Show HN](https://news.ycombinator.com/show)", but the hard lessons drilled into me have ~~haunted~~ inspired me to explore better API design, TDD, modular CSS patterns with PostCSS and BEM, and a renewed focus on ES6+ JavaScript fundamentals for future projects I'm gestating.

Let's dig in!

# Tooling

If you're going to build a good table, you'd better know what tools to use when, why, and how. Otherwise your food will slide into your lap.

## Linting with Standard JS (Use with caution)

I was skeptical of [Standard JS](http://standardjs.com/) at first,
but I gave it a chance at the recommendation of Brian Holt in his workshop and really enjoyed the simplicity.

A lot of [very](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20&%20grammar/ch5.md#error-correction) [smart](http://www.2ality.com/2011/05/semicolon-insertion.html) [people](https://google.github.io/styleguide/javascriptguide.xml#Semicolons) caution (with good reason) that omitting semi-colons and relying on JavaScript's [Automatic Semicolon Insertion](https://www.ecma-international.org/ecma-262/5.1/#sec-7.9) feature should be avoided. For companies with a large team of developers, relying on ASI adds another layer of risk.

That said, the common [gotchas](http://standardjs.com/rules.html#semicolons) of omitting semi-colons are well known and Standard has lint rules to catch those mistakes, assuming that all of your code is always linted. Continuous integration tools like TravisCI can run a final lint check for you before allowing the code to be deployed to staging. If any lint errors are present, the build will fail and it won't get deployed. I think Standard is fine for personal projects and smaller companies, as long as linting is strictly enforced.

For safety, [Semistandard](https://github.com/Flet/semistandard) (Standard plus semicolons) is available, and you'll still get all of the benefits of a simple, effective style guide that you don't need to spend time tweaking. This is good for having the peace of mind of knowing the risk of ASI bugs is off the table.

### Why Standard (or Semistandard)?

Eslint is a powerful tool, but I'd spent more time than I'd like to admit tweaking linting rules and wrangling `.eslintrc` files .

Standard JS takes all of that choice out of the equation and enforces a simple, reliable style guide that CAN'T be changed. If you change it with custom lint rules, than you're not coding in Standard Style.

This idea is in the spirit of [PEP 8](https://www.python.org/dev/peps/pep-0008/) from Python. If you know PEP 8 well, you'll have an easy time reading other people's Python code. Standard seeks to offer that same spirit of consistency for JavaScript.

While I don't think it would be good for JavaScript to officially standardize any single style guide, Standard offers a set of eslint rules that force you to set-it-and-forget-it. This saves you valuable brain cycles.

### Using Standard Without React

Standard itself is very easy to [install and use](https://github.com/feross/standard#install). The docs are straightforward.

Running Standard alone in the terminal works, but the default lint error output is hard to read in the terminal.

Instead of using Standard directly, you can install a package called `snazzy`, which will give you nicely formatted results with colors.

If you have a new project, here's what you can do:

Install:

{% highlight bash %}
$ yarn add -D snazzy
{% endhighlight %}

> If you already have standard installed globally, install `standard` as a dev dependency to work with snazzy in your project without errors:
`yarn add -D standard snazzy`. The smarties behind Yarn [discourage](https://yarnpkg.com/en/docs/cli/add#toc-caveats) using global dependencies in most cases, so keep them local whenever possible so your projects can stay portable between machines. NPM works just fine too if you're not using yarn.

Add a lint script to `package.json`:

{% highlight json %}
{
  "scripts": {
    "lint": "snazzy"
}
{% endhighlight %}

> If you're using `npm`, mute the annoying `err!` messages by adding `exit 0` to your script: `"lint": "snazzy; exit 0`

Run it:

{% highlight bash %}
$ yarn run lint
{% endhighlight %}

That's it! Standard will magically find your JavaScript (excluding `node_modules`) and return your lint errors, or nothing if everything passes.

### Using Standard With React

Using Standard with React will require an eslint config file, but it's still very easy.

install:

{% highlight bash %}
$ yarn add -D eslint-config-standard eslint-config-standard-react eslint-plugin-promise eslint-plugin-react eslint-plugin-standard
{% endhighlight %}

> If you have `eslint` installed globally, add `eslint` to the above command so that your config points to the local copy of `eslint` to avoid errors.

Create a file called `.eslintrc` in your root directory and add this:

{% highlight json %}
{
  "extends": ["standard", "standard-react"]
}
{% endhighlight %}

Add an `eslint` command with your source code directory to your lint script:

{% highlight json %}
{
"scripts": {
  "lint": "eslint src"
}
{% endhighlight %}

Unlike `standard` or `snazzy`, you need to specify where eslint should look for your `.js` files. In the script above, eslint will check a directory called `src`. You can add multiple directories separated by spaces.

What if you use extensions like `.jsx` or `.es6`? `eslint` has a flag for that:

{% highlight json %}
{
  "scripts": {
    "lint": "eslint --ext .js --ext .jsx src"
}
{% endhighlight %}

The CLI has many more options depending on your needs. [The docs are very helpful](http://eslint.org/docs/user-guide/command-line-interface).

Finally, run it:

{% highlight bash %}
$ yarn run lint
{% endhighlight %}

Happy linting!

You can also delegate linting to `webpack` using `eslint-loader`, so can see lint errors every time a new bundle is compiled. More on that in a bit.

I've turned off linting in Sublime Text because I've found it more distracting then helpful. Plus, it creates a latency while saving. If you don't like linting in a terminal, eslint plugins are available for the major text editors.

## Webpack 2

In a nutshell, Webpack bundles up your application's assets into a bundle files to reduce HTTP requests. This lets you import things (js, jpg's, css) into your JavaScript modules, and Webpack will bundle up everything smartly so that your dependency tree remains intact.

Very recently, Webpack 2 was [finally released](https://medium.com/webpack/webpack-2-2-the-final-release-76c3d43bf144#.plxnf4o9g) and it's awesome!

They've made the [migration process](https://webpack.js.org/guides/migrating/) almost painless. Webpack 2 will validate your `webpack.config.js` for you, so you don't need `webpack-validator` anymore. If you mess something up, Webpack will give you a helpful error message outlining what doesn't match their API, and how you can fix it.

The [documentation](https://webpack.js.org/configuration/) is very well thought out and organized so I encourage you to check it out if you've never used Webpack before, or haven't upgraded from version 1.

While webpack seems to perform a basic task (files in -> bundle(s) out), its configuration can get complex depending on what you need it to do. The documentation for version 2 is thankfully much more clear and simplified than version 1, so go through the concepts and guides on [webpack.js.org](https://webpack.js.org/) if you don't know where to start. It will get you up and running in no time.

### Optimizing Your Bundle(s)

As your project grows, so will your bundle. It will probably get huge. The development version of Vote had a bundle that was over 3 MB before optimizing. For production, it now has a `vendor.js` bundle for larger dependencies, and a `bundle.js` bundle for the app itself. Each bundle weighs in under 200 kb minfied and gzipped. They're still heave, but much better than before. The main bundle can get broken up even further with [code spitting](https://webpack.js.org/guides/code-splitting/) so the client will only request of the pieces of the app it needs for the current view, instead of the entire app at once.

Before configuring the `vendor.js` bundle for Vote, I first had to figure out which dependencies were bulking up the `bundle.js` file the most.

#### Analyzing a Bundle with `webpack-bundle-analyzer`

One of the coolest plugins I've come across for Webpack is the [webpack-bundle-analyzer](https://www.npmjs.com/package/webpack-bundle-analyzer). It gives you a highly-interactive visualization of your bundle(s) to show you the elephants in the room.

It's as easy as installing:

{% highlight bash %}
$ yarn add -D webpack-bundle-analyzer
{% endhighlight %}

and including it with your plugins in your Webpack config:

{% highlight javascript %}
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin

module.exports = {

// rest of config...

  plugins: {
    new BundleAnalyzerPlugin()
  }
}

{% endhighlight %}

Now, anytime you build a new bundle, `BundleAnalyzerPlugin` will spin up a server, and open your browser to a visualization of your bundle(s) and dependencies scaled to size. The plugin can take a config object as a parameter, but the defaults should be just fine for most cases. The config options are in the [docs](https://github.com/th0r/webpack-bundle-analyzer). To "turn it off," you can simply comment out the plugin.

For Vote, I used this tool to determine which vendor dependencies should be extracted into a `vendor.js` bundle.

#### Splitting Bundles

A lot of quick wins can be gained by splitting up your bundle. To start, putting your 3rd party libraries in a `vendor.js` bundle will not only reduce the file size of the two, but you'll also be able to take advantage of caching 3rd party dependencies that likely won't change nearly as often as your application's code. Your users would only need to download your bulky dependencies once, and they'll be ready to go almost immediately on future visits. Boom! Just be sure to include a [chunkHash](https://webpack.js.org/guides/caching/) with each bundle so that browsers won't serve old cached bundles after you deploy a new build.

The implementations for splitting up bundles can get [deep](https://survivejs.com/webpack/building/splitting-bundles/) very quickly, so split them up based on how you expect your app to grow, and split more deeply as you need to.

Some great advice on how to shrink down certain dependencies, and deal with multiple copies of dependencies with different versions can be found in [this great post](https://medium.freecodecamp.com/manually-tuning-webpack-builds-284923f47f44#.799l57uja).

After splitting out your 3rd party dependencies, you can also take advantage of [code splitting](https://webpack.js.org/guides/code-splitting-import/) for your application code as well so your users only need to download the necessary code for each "page" of your single page app, instead of the whole thing at once.

For simplicity, I decided to add just the largest libraries to Vote's `vendor.js` bundle. `d3`, `jquery`, `react`, `react-dom` and `moment`.

Those dependencies alone made the main `bundle.js` much smaller, without an overly-large `vendor.js` file.

If you have a lot more dependencies and modules in a large application, you can spit up your bundles in many different ways, but in this case for Vote, I'm not going to spend the time code splitting further since this app is not getting any larger.

#### Optimizing Bundles for Production

Making Webpack 2 builds for [production](https://webpack.js.org/guides/production-build/) can be as easy as running `webpack -p`.

That one flag will minify all of your JavaScript with Webpack's `UglifyJsPlugin`, set all of your loaders' minimize option to `true`, and set the Node environment variable to 'production', allowing your code's production optimizations can take effect. This is important for React as it has a lot of production optimizations.

The `webpack -p` command is very handy for many situations, but for Vote, I wanted to gzip my JavaScript to shrink the bundles even further. That required a bit of config.

A quick way to cleanly create a production config file for webpack is to use `webpack-merge`. This allows you to combine a separate config file with your main `webpack.config.js` file to limit code duplication and keep your config files clean and easy to extend. To keep things simple, I decided to simply add a `webpack.prod.js` file for production builds, and use my `webpack.config.js` file alone for development builds.

to install:

{% highlight bash %}
$ yarn add -D webpack-merge
{% endhighlight %}

Then, instead of exporting your config objects directly, you'll need to return the config objects as functions. Easy.

`webpack.config.js`:

{% highlight javascript %}
const webpack = require('webpack')

module.exports = function () {
  return {
    // config...
  }
}
{% endhighlight %}

The production config function will actually use `webpack-merge` to include `webpack.config.js`'s config properties as `commonConfig`.

{% highlight javascript %}
const webpack = require('webpack')
const CompressionPlugin = require('compression-webpack-plugin')
const webpackMerge = require('webpack-merge')
const commonConfig = require('./webpack.config.js')

module.exports = function (env) {
  return webpackMerge(commonConfig(), {
    plugins: [
      new webpack.LoaderOptionsPlugin({
        minimize: true,
        debug: false
      }),
      new webpack.DefinePlugin({
        'process.env': {
          'NODE_ENV': JSON.stringify('production')
        }
      }),
      new webpack.optimize.UglifyJsPlugin({
        beautify: false,
        compress: {
          screw_ie8: true
        },
        comments: false
      }),
      new CompressionPlugin({
        asset: "[path].gz[query]",
        algorithm: "gzip",
        test: /\.js$/,
        threshold: 10240,
        minRatio: 0.8
      })
    ]
  })
}

{% endhighlight %}

`webpackMerge` runs `commonConfig()` in its argument and receives `webpack.config.js`'s properties.

The first three plugins are mostly identical to what `webpack` includes for you by default when passed the `-p` flag, besides some extra options in `UglifyJsPlugin` here, but now I was able to add `CompressionPlugin` to gzip the `.js` bundles spit out by Webpack. We'll get into how gzipped files can be served from Express, but \*Spoiler Alert\* you can use [`express-static-gzip`](https://www.npmjs.com/package/express-static-gzip) instead of `express.static`. `express-static-gzip` is a wrapper over `express.static` that lets you serve static gzip files from a directory.

There are likely better ways to gzip files automatically from `niginx`, but my `nginx`fu is lacking on that front.

Now, you can add an npm script to build your production bundle:

{% highlight javascript %}
"scripts": {
  "build-prod": "webpack --config webpack.prod.js"
}
{% endhighlight %}

### Tree-Shaking

Another new feature in Webpack 2 is tree-shaking. Since ES6 modules are now supported, Webpack 2 can check for exports in your code during the bundling step, determine if they're imported anywhere, and remove the exports that no other files import. Then, when you uglify your JavaScript, all of the now-dead export-less code gets stripped away. Since the unused modules lost their export calls, the code inside never gets touched. Think of this like the name "tree-shaking" implies. You have a dependency tree of code, and the branches of that tree that are never touched just fall off during the uglify step. Webpack will show you verbose logs of the code being removed during the uglify step.

There is a problem with this if you use Babel's `es2015` preset, but its easy to fix.

Just set `modules` to false in your `.babelrc`:

{% highlight javascript %}
"presets": [
    "react",
    ["es2015", { "modules": false }]
  ],
{% endhighlight %}

Babel's `es2015` preset will convert your your ES6 modules to CommonJS modules by default, so just turn it off and your ES6 modules will be respected.

### Linting with Webpack

Remember Standard? You can use [`eslint-loader`](https://github.com/MoOx/eslint-loader) to lint your code before Webpack bundles everything. I did this based on Brian Holt's recommendation in Complete Intro to React.

First install:

{% highlight bash %}
yarn add -D eslint-loader
{% endhighlight %}

Then, add this to your config:

{% highlight javascript %}
module.exports = {
  // rest of config...
  module: {
    rules: [
      {
        test: /\.js?$/,
        enforce: 'pre',
        loader: 'eslint-loader',
        exclude: /node_modules/
      }
    ]
  }
}
{% endhighlight %}

Note `enforce: 'pre`. That will allow your code to be linted *before* webpack's build step.

#### One Caveat

Keep in mind that `eslint-loader` will only see the application code that gets bundled, so your server code will still need to be linted separately. If you're using Standard and not Semistandard, this is doubly important to protect yourself from ASI related errors. Again, TravisCI can add a final lint check to your build before deploying.

# The Client

Vote is a React application that leans heavily on Redux to share state across many components.

The skeleton of the application is based on Brian Holt's Complete Intro To React workshops, though I did a heavy refactor of the Redux logic as the features got more complex. I ended up using a pattern similar to [Ducks](https://github.com/erikras/ducks-modular-redux).

## React

Why React?

I've been a big fan of React because its very stripped down, and funnels you into making good decisions most of the time. It encourages functional programming concepts and has great patterns for handling immutable state and data flow.

Is React good for everything?

Of course not. There is no such thing as a one-size-fits-all framework. React work awesomely well for a lot of situations and teams, apps big and small, but if you have a large team of developers working independently on the same app, a more structured framework like Angular 2 or Ember may better serve a team's needs depending on the company culture. React is very powerful while staying out of your way, but if a team of 10 or 15 developers are working on the same application independently, many different styles of solving the same common problems can make things harder to maintain and tie together, especially as an app grows. Even if React is a lot more fun, easier to work with, and faster to develop with, a framework with more structure and "magic" can potentially be more maintainable over the long term for big projects, even if it is a pain to wrestle with at time. Like a lot of things in computer science, "it depends." I've heard it said that when choosing a framework to use, it's good to pick the one with the least potential of being the worst possible choice down the line.

The fact that Redux itself is so simple to reason about and test, and that React UI markup can be expressed so cleanly with JavaScript, those are reasons enough for React and Redux to be my go-to libraries for personal projects. If you know JavaScript, React will feel very natural. React components are just functions that return UIs.

### `React.createClass` vs ES6 classes

Since [React v0.13](https://facebook.github.io/react/blog/2015/03/10/react-v0.13.html), ES6 classes have become the defacto standard for building stateful React components with some [helpful encouragement](https://medium.com/@dan_abramov/how-to-use-classes-and-sleep-at-night-9af8de78ccb4#.8he89ybjd) from Dan Abramov.

I recently used this style for the first time while refactoring my Free Code Camp Wikipedia search project, [Spiffy Wikipedia](https://github.com/itxchy/FCC-spiffy-wikipedia), with `create-react-app` (it's deployed [here](https://spiffywikipedia.matttrifilo.com/)). At first it felt annoying to have to bind each method with the constructor's contextual `this`, but if you think about it, this a win from a performance standpoint since React doesn't need to use any "magic" to bind `this` for you like in `React.createClass` functions.

You can even use a `react` replacement like `preact` or `inferno` which strip away `react`'s `createClass` logic entirely (plus a lot of other things). The stripped-down half-siblings of React are mostly compatible with React's APIs, and they're better optimized for mobile devices. The developers behind Inferno have [good reasons for prioritizing mobile](https://github.com/infernojs/inferno#but-why).

Another performance gain can be achieved by using [stateless functional components](https://hackernoon.com/react-stateless-functional-components-nine-wins-you-might-have-overlooked-997b0d933dbc#.8ebtfvkv9) as much as you can until you need to use state, methods, or React lifecycle methods.

For Vote, I used the `React.createClass` style for stateful components because that's what I'd been used to, and how Brian Holt taught Complete Intro To React.

Practically speaking, I don't really have a preference of one stateful component style over the other at this point in time, but I do like how I don't have to worry about forgetting a coma when using ES6 classes. Manually binding methods isn't a big deal. Looking at the broader community, ES6 classes undeniably the determined direction that most developers have committed to. It's probably best not to fight the tide, and ensure that your open source code is as readable as possible for the majority of React developers who are used to ES6 classes. Empathy is a virtue. That said, I doubt that Facebook would deprecate `React.createClass` in the foreseeable future since so many codebases rely on it.

### PropTypes

The true value of React's [PropTypes](https://facebook.github.io/react/docs/typechecking-with-proptypes.html) really shine while building larger apps.

Take this example from Vote's Create A Poll page component:

{% highlight javascript %}
import React from 'react'
import NewPollTitle from './NewPollTitle'
import PendingPollOptions from './PendingPollOptions'
import SaveOrReset from './SaveOrReset'
import { connect } from 'react-redux'
import {
  updateOption,
  setNewPollTitle,
  setTitleEditable,
  submitNewPoll,
  resetNewPoll,
  resetPollSaved
} from '../../redux/modules/createNewPoll'
const { object, func, string, bool, array } = React.PropTypes

const CreateAPoll = React.createClass({
  propTypes: {
    poll: object,
    dispatchUpdateOption: func.isRequired,
    newPollTitle: string,
    titleEditable: bool,
    dispatchSetNewPollTitle: func.isRequired,
    dispatchSetTitleEditable: func.isRequired,
    newPollOptions: array,
    dispatchSubmitPoll: func.isRequired,
    dispatchResetNewPoll: func.isRequired,
    dispatchResetPollSaved: func.isRequired,
    pollSaved: string,
    user: object
  },
  componentWillReceiveProps (nextProps) {
    if (nextProps.pollSaved) {
      this.context.router.push(`/v/${nextProps.pollSaved}`)
      this.props.dispatchResetPollSaved()
    }
  },
  render () {
    const newPoll = true
    return (
      <div>
        <h1 className='view-title text-center'>Create a New Poll</h1>
        <NewPollTitle
          newPollTitle={this.props.newPollTitle}
          titleEditable={this.props.titleEditable}
          dispatchSetNewPollTitle={this.props.dispatchSetNewPollTitle}
          dispatchSetTitleEditable={this.props.dispatchSetTitleEditable}
        />
        <PendingPollOptions
          poll={this.props.poll}
          dispatchUpdateOption={this.props.dispatchUpdateOption}
        />
        <SaveOrReset
          newPollTitle={this.props.newPollTitle}
          newPollOptions={this.props.newPollOptions}
          dispatchResetNewPoll={this.props.dispatchResetNewPoll}
          dispatchSubmitPoll={this.props.dispatchSubmitPoll}
          user={this.props.user}
          poll={this.props.poll}
          newPoll={newPoll}
          pollID={null}
        />
      </div>
    )
  }
})

CreateAPoll.contextTypes = {
  router: object.isRequired
}

const mapStateToProps = (state) => {
  return {
    poll: state.newPoll,
    newPollTitle: state.newPoll.newPollTitle,
    titleEditable: state.newPoll.titleEditable,
    newPollOptions: state.newPoll.newPollOptions,
    pollSaved: state.newPoll.pollSaved,
    user: state.user
  }
}

const mapDispatchToProps = (dispatch) => {
  return {
    dispatchUpdateOption (newOptions) {
      dispatch(updateOption(newOptions))
    },
    dispatchSetNewPollTitle (pollTitle) {
      dispatch(setNewPollTitle(pollTitle))
    },
    dispatchSetTitleEditable (bool) {
      dispatch(setTitleEditable(bool))
    },
    dispatchResetNewPoll (newPoll) {
      dispatch(resetNewPoll())
    },
    dispatchSubmitPoll (newPoll) {
      dispatch(submitNewPoll(newPoll))
    },
    dispatchResetPollSaved () {
      dispatch(resetPollSaved())
    }
  }
}

export default connect(mapStateToProps, mapDispatchToProps)(CreateAPoll)

{% endhighlight %}

There is a lot going on here, but just take a look at all of the `propTypes` checking the props coming in from Redux.

PropTypes allow you to type check your props to ensure your components receive the types they're expecting. This can catch a lot of bugs early. Using `isRequired` will throw a warning if an essential prop doesn't show up.

Beyond type checking, the `propTypes` property is great for documenting all of the props that your component is expecting.

If you're using ES6 class syntax for React, you can include your `propTypes` as a `static` property. Here's an [example](https://github.com/reactjs/redux/blob/85e2368ea9ff9b308fc873921ddf41929638f130/examples/todomvc/src/components/Header.js#L5).

Note that `static` properties are still only at [Stage-2](https://github.com/hemanth/es-next#class-property-declarations) in the [TC39 process](http://www.2ality.com/2015/11/tc39-process.html), so you'll need an extra Babel plugin called [`transform-class-properties`](https://babeljs.io/docs/plugins/transform-class-properties/) to transpile them.

Otherwise, you can tack on `propTypes` to your Component `class`
separately:

{% highlight javascript %}
import React, { Component } from 'react'
const { string } = React.PropTypes

class Greeting extends Component {
  constructor (props) {
    super (props)

    this.greet = this.greet.bind(this)
  }

  greet () {
    return `${this.props.greetingMessage} ${this.props.name}!`
  }

  render () {
    return (
      <h1>{this.greet}</h1>
    )
  }
}

Greeting.propTypes = {
  greetingMessage: string.isRequired
  name: string.isRequired
}

export default Greeting
{% endhighlight %}

Note the casing difference in `React.PropTypes` vs `Greeting.propTypes`.

### Component Hierarchy

The official React docs have a great guide about how to [think about your component structure](https://facebook.github.io/react/docs/thinking-in-react.html).

One of my favorite qualities of React is the ability to compose components in your render function as JSX, effectively treating them as if you're using custom HTML elements containing whatever you'd like, taking in any attributes you'd like. JSX is just JavaScript, so you can pass in JaveScript expressions to component props as well as regular HTML attributes. These JavaScript expressions can include arrow functions as well.

>Digression: One great handy use for arrow functions in React's synthetic DOM event attributes is when you want to pass an argument to an `onClick` event. The native `onclick` handler only accepts a function reference with no parameters. `onclick` will pass along the click event object to the function its calling as that function's parameter. You can get around this easily in React:

{% highlight javascript %}x
<a
  className='btn btn-danger delete-button'
  onClick={() => this.deleteOption(index)}
  aria-label='Delete' >
  <i className='fa fa-trash-o' aria-hidden='true' />
</a>
{% endhighlight %}

This way, I was able to use an arrow function inside of `onClick`'s JavaScript expression to pass `index` to `this.deleteOption(index)` as the arrow function's implicit return value. `index` will be the index value from a `map` function encapsulating this link.

Decisions about how to break up your UI into components can get subjective, but it makes sense to use the [Single Responsibly Principle](https://en.wikipedia.org/wiki/Single_responsibility_principle) as mentioned in the docs. In practice, this can get difficult as applications grow, but simple code doesn't mean easy code.

#### Spiffy Wikipedia Example

For a simple example, let's put Vote aside for a moment and look at my other recently-refactored React project, [Spiffy Wikipedia](https://github.com/itxchy/FCC-spiffy-wikipedia). This project simply allows you to search Wikipedia for a topic using its API, and the see some aesthetically pleasing results.

Component tree:

{% highlight markdown %}
- App
  + Header
  + SearchBar
  + SearchResults
    * Result
{% endhighlight %}

##### Container Components

`ReactDOM` renders [`App`](https://github.com/itxchy/FCC-spiffy-wikipedia/blob/master/src/components/App.js):

{% highlight javascript %}
class App extends Component {

// ... check the GitHub repo for the complete code

  render () {
    return (
      <div className='App'>
        <Header />
        <SearchBar onSearchSubmit={this.handleSearchSubmit} />
        <SearchResults searchResults={this.state.data} error={this.state.error} />
      </div>
    )
  }
}
{% endhighlight %}

App's responsibility as a [container component](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0#.qlp6sobvr) is to handle search requests from `SearchBar`, and pass the results down to `SearchResults` after performing an AJAX call to Wikipedia's API.

As Dan Abromov puts it, container components are "concerned with *how things work.*"

Notice that this component only displays other components. The main concern of `App` is with handling data and passing it down to its children.

##### Presentational Components

`Header`:

{% highlight javascript %}
import React from 'react'
import './Header.css'

function Header (props) {
  return (
    <header>
      <h1>Spiffy Wikipedia</h1>
    </header>
  )
}

export default Header

{% endhighlight %}

Header is a very simple stateless functional component. It shows the header. Notice the `Header.css` import. It may seem trivial to make a separate component for such a simple piece of UI, but now, anytime I might want to change the header's look, I know exactly where to go. Everything about the header's presentation, from markup to styling, is encapsulated right here inside this component. It has one responsibility.

##### Time to Refactor

`SearchBar`:

{% highlight javascript %}
import React, { Component } from 'react'
import './SearchBar.css'
import { Form, FormGroup, FormControl, Button } from 'react-bootstrap'

class SearchBar extends Component {

// ... check the GitHub repo for the complete code

  render () {
    return (
      <div className='form-container'>
        <Form inline onSubmit={this.handleSubmit}>
          <FormGroup controlId='searchInput' bsSize='large'>
            <FormControl
              type='text'
              value={this.state.searchText}
              placeholder='What are you looking for?'
              className='search-input'
              onChange={this.handleSearchInputChange}
            />
          </FormGroup>
          <Button type='submit' bsSize='large' id='search-submit'>
            Search
          </Button>
        </Form>
        <a className='chance-link' href='https://en.wikipedia.org/wiki/Special:Random'>Take a chance!</a>
      </div>
    )
  }
}

SearchBar.propTypes = {
  onSearchSubmit: func.isRequired
}

export default SearchBar

{% endhighlight %}

There is a code smell here.

SearchBar is currently a presentational component with one responsibility, which is to present the search bar. It's doing that single responsibility, but with quite a bit of markup to put a form together, as well as a link to a random Wikipedia article.

Do you keep everything encapsulated here, or do you break this up with components for "SearchForm", "Button" inside "SearchForm", and "ChanceLink"? This is where things get subjective. If you're not careful, you could end up making a component for every synthetic DOM element. On the other hand, it's good for readability and maintainability to keep components as simple as possible.

One easy compromise would be to make a `SearchForm` component to be responsible for the form itself, and leave the chance link in `SearchBar` since its only one simple link element:

{% highlight javascript %}
class SearchBar extends Component {
  constructor (props) {
    super(props)
    this.state = {
      searchText: ''
    }
    this.handleSearchInputChange = this.handleSearchInputChange.bind(this)
    this.handleSubmit = this.handleSubmit.bind(this)
  }

  handleSearchInputChange (event) {
    this.setState({ searchText: event.target.value })
  }

  handleSubmit (event) {
    event.preventDefault()
    if (!this.state.searchText) {
      return
    }
    this.props.onSearchSubmit(this.state.searchText.trim())
    this.setState({ searchText: '' })
  }
  render () {
    return (
      <div className='form-container'>
        <SearchForm
          handleSubmit={this.handleSubmit}
          searchText={this.state.searchText}
          handleSearchInputChange={this.handleSearchInputChange} />
        <a className='chance-link' href='https://en.wikipedia.org/wiki/Special:Random'>Take a chance!</a>
      </div>
    )
  }
}

SearchBar.propTypes = {
  onSearchSubmit: func.isRequired
}
{% endhighlight %}

Now that render function is much cleaner. I included the rest of the code here to illustrate how SearchBar's methods and state can be passed down as props.

##### Data Flow

As events get triggered in the `SearchForm` component, SearchForm itself doesn't alter data or handle events. SearchForm simply passes events up to SearchBar, either an input value change, or a submit event. If SearchBar gets an input change event from `this.handleSearchInputChange`, called from SearchForm, SearchBar updates its state, and passes that new state down to SearchForm's `searchText` prop as the new data. SearchForm can than update the form with the new data it receives. When SearchForm's form is submitted, it can call SearchBar's `this.handleSubmit` method, which SearchForm received as a `handleSubmit` prop from SearchBar. SearchBar will handle the submit by passing the form data up to App using App's `handleSearchSubmit` method, which App passed down to SearchBar as a prop called `onSearchSubmit`. SearchBar doesn't care about the details of how to searches are actually submitted. That's `App`'s responsibility.

The important takeaway is that data always moves in one direction. This is the essence of one-way data-binding. If anything breaks, its easy to trace where  something went wrong because data only moves in a single direction when an event happens. Typing something into the form doesn't update the DOM form directly like you'd expect natively. Instead, your keystroke is triggering a change event, which eventually culminates with React rendering the form's text from its own state. This happens so fast, it feels native. This behavior happens in what's called a [controlled component](https://facebook.github.io/react/docs/forms.html). React is handling the form state, instead of allowing the native DOM to handle form state in the browser. The process of debugging errors in this sort of flow is a lot easier than in other paradigms where data can be passed in both directions. Two-way data binding makes it hard to determine the source of bugs if you don't know where the data is coming from when a bug occurs. In large, complex apps, this can be a nightmare. Thankfully, Angular has moved passed that in later versions, and many other frameworks have embraced one-way data-binding.

[React Dev Tools](https://facebook.github.io/react/blog/2015/09/02/new-react-developer-tools.html) even allow you to watch state updates in real time!

##### Escalating to Redux

Now, what if I decided to add a dropdown component of look-ahead search results SearchForm?

It would make sense to include the dropdown's UI as a separate component, but what if it needs to call [`loadWikiData`](https://github.com/itxchy/FCC-spiffy-wikipedia/blob/master/src/components/App.js#L28) from `App.js`  every 400 ms to load results?

Let's reference the updated component tree:
{% highlight markdown %}
- App (WikiContainer)
    + SearchBar
      * SearchForm
        - LookAheadDropdown
          + LookAheadResult
    + SearchResults
      * Result
{% endhighlight %}

We don't need to flesh out the new components in code to realize we'd have a problem. To pull this off, we'd need to pass `App`'s `loadWikiData` method down as a prop through `SearchBar`, into `SearchForm`, and possibly even to `LookAheadDropdown` depending on whether `SearchForm` gets refactored to include `handleSearchInputChange` and `handleSubmit` instead of [SearchBar](https://github.com/itxchy/FCC-spiffy-wikipedia/blob/master/src/components/SearchBar/SearchBar.js) to further clarify each components' single responsibility. But that's not all! We'd need to pass [`state.data`](https://github.com/itxchy/FCC-spiffy-wikipedia/blob/master/src/components/App.js#L13) from `App` down the component tree to at least `SearchForm` as well.

This is an example of the data tunneling problem. As the components get nested deeper, so do the layers of components that data and events need to pass through. This is when Redux's predictable complexity becomes more desirable than the unpredictable complexity of data tunneling which can grow over time.

## Redux

In essence, Redux allows you to store and manage all of your React state outside of your React components.

It's uses a variation of the [Flux](https://facebook.github.io/flux/docs/in-depth-overview.html#content) architecture from Facebook. Flux is a pattern, not an implementation, that describes a "store", or multiple "stores", which are objects that hold on to your immutable state, "actions" which trigger state changes, and "dispatchers" which trigger actions based on UI events, or other events. When a store is updated, the view layer (usually React) receives the new state as props, and updates its UI with the changes. Flux has a lot of other pieces to it which I won't dig into here since you'll likely be using a Flux implementation library like Redux. Flux is complicated because it solves a much more complicated problem. Facebook's UI code is very complex, with many hundreds (thousands?) of deeply nested components, many needing to share props and state. Just imagine building Facebook UIs with React alone.

There are [many libraries](https://github.com/voronianski/flux-comparison) implementing the Flux pattern, but Redux has been the most widely used lately and for good reason. Instead of allowing multiple stores, Redux uses a single store for all of your state. Any time an action is dispatched, a reducer function takes in the action plus the current state, and returns a new state object with the changes, not a mutated form of the state object which was passed in as a parameter. This allows for powerful features like time-travel debugging in [Redux Developer Tools](https://github.com/gaearon/redux-devtools), which does exactly what you'd expect. Since reducers create an entirely new state object each time, it's possible to track those unique objects over time, and pass your application back and forth between states with Redux Dev Tools. This single direction of data flow into the single store object makes debugging very easy since its easy to trace which reducers update state in order over time, and to see how each update affects the state object.

For big apps, Redux allows you to decouple your components so they don't need to worry about the flow of state, state-altering methods, or events being passed through the component tree. It all flows in and out of Redux, a la carte style.

### When Do You Need Redux?

Not all apps need Redux, and you shouldn't use it if you're not running into the problems it's meant to solve. There are trade-offs.

On one hand, Redux allows you share state between components easily, and keep components decoupled and portable.

On the other hand, Redux adds complexity and weight to your app, making it more tedious to add features and maintain. It also increases your app's surface area for bugs.

At what point do you *need* Redux?

Many people don't mind data-tunneling for small to mid-size apps, but its probably a good idea to bring in Redux once you find yourself with state and props that need to be passed at least two layers deep in multiple parts of your application. As your app grows, those complexities will start to snowball, and refactoring will become more and more difficult.

If, on the other hand, you have an app with one component a few layers deep that needs to tunnel some state, then you'll probably be fine just handling the data-tunneling in that instance. The added complexity of Redux would outweigh the complexity of maintaining one data-tunneling occurrence without Redux.

For Vote, adding Redux was a no-brainer. Many components need access to the `user` object in state, which alone would have been a nightmare to pass between components. A higher-order component could have been an option, but there are a number of other bits of state and methods, like `flashMessage`'s state-altering methods, which are used in multiple components in different branches of the component tree. Using higher-order components for everything would get hairy fast.

The added complexity of Redux didn't hold a candle to the added complexity without it.

### A Ducks Pattern Variant

At first, my Redux code lived in a single `store.js` file. It didn't take long for the file to get hairy.

After some research, I read about the [Ducks](https://medium.com/@scbarrus/the-ducks-file-structure-for-redux-d63c41b7035c#.yawhsa7sx) Pattern which looked very promising.

I ended up with a variation of that pattern which worked out well:
{% highlight markdown %}
/redux
  /modules
    module.js
    ...
  Store.js
  rootReducer.js
{% endhighlight %}

Very simple!

Here's Store.js:

{% highlight javascript %}
import { applyMiddleware, compose, createStore } from 'redux'
import thunk from 'redux-thunk'
import rootReducer from './rootReducer'

export const store = createStore(rootReducer, compose(
    applyMiddleware(thunk),
    typeof window === 'object' && typeof window.devToolsExtension !== 'undefined' ? window.devToolsExtension() : (f) => f
))

export default store
{% endhighlight %}

The `Store.js` file initializes the store with middleware and the rootReducer. Thanks again to Brian Holt for teaching that handy devToolsExtension one-liner. Before that, I used `remote-dev-tools` to spin up a separate server to run the Redux Dev Tools to prevent server-side errors.

rootReducer.js:

{% highlight javascript %}
import { combineReducers } from 'redux'
import flashMessages from './modules/flashMessage'
import newPoll from './modules/createNewPoll'
import user from './modules/auth'
import allPolls from './modules/getAllPolls'
import newVote from './modules/submitVote'
import clientFormValidation from './modules/clientFormValidation'
import userSignupRequest from './modules/userSignupRequest'
import userPolls from './modules/getUserPolls'
import singlePoll from './modules/getSinglePoll'
import editPoll from './modules/editPoll'
import deletedPoll from './modules/deletePoll'

export default combineReducers({
  flashMessages,
  newPoll,
  user,
  allPolls,
  newVote,
  clientFormValidation,
  userSignupRequest,
  userPolls,
  singlePoll,
  editPoll,
  deletedPoll
})

{% endhighlight %}

`rootReducer.js` combines all of the reducer slices from modules into one root reducer.

One important note is that the name of the exported reducer slice inside each module determines the name of the module's individual state object in the store.

Let's look at the `createNewPoll.js` module:

{% highlight javascript %}
import axios from 'axios'
import { addFlashMessage } from './flashMessage'

export const DEFAULT_STATE = {
  newPollTitle: '',
  titleEditable: true,
  newPollOptions: [
    '',
    ''
  ],
  pollSaved: null
}

// ******* Action Types *******

const SET_NEW_POLL_TITLE = 'SET_NEW_POLL_TITLE'
const SET_NEW_TITLE_EDITABLE = 'SET_TITLE_EDITABLE'
const UPDATE_OPTION = 'UPDATE_OPTION'
const RESET_NEW_POLL = 'RESET_NEW_POLL'
const POLL_SAVED = 'POLL_SAVED'
const RESET_POLL_SAVED = 'RESET_POLL_SAVED'

// ******* Action Creators *******

/**
 * Sets state.newPollTitle
 *
 * @param {string} pollTitle
 */
export function setNewPollTitle (pollTitle) {
  return { type: SET_NEW_POLL_TITLE, value: pollTitle }
}
/**
 * Sets state.titleEditable
 *
 * @param {boolean} bool
 */
export function setTitleEditable (bool) {
  return { type: SET_NEW_TITLE_EDITABLE, value: bool }
}
/**
 * Sets state.newPollOptions
 *
 * @param {array} updatedOptions - An array of at least 2 strings
 */
export function updateOption (updatedOptions) {
  return { type: UPDATE_OPTION, value: updatedOptions }
}
/**
 * Resets state to DEFAULT_STATE
 */
export function resetNewPoll () {
  return { type: RESET_NEW_POLL }
}
/**
 * Sets state.pollSaved as a new Poll's ID
 *
 * @param {string} pollId - A new poll's mongoDB _id
 */
export function pollSaved (pollId) {
  return { type: POLL_SAVED, pollId }
}
/**
 * Sets state.pollSaved as null
 */
export function resetPollSaved () {
  return { type: RESET_POLL_SAVED }
}
/**
 * Submits a new poll to the server
 *
 * @param {object} newPoll - an object containing a new poll's title, at least two
 * options, and the owner (user). Sample: { title: 'What number?', options: ['one', 'two'], owner: 'Lloyd'}
 *
 */
export function submitNewPoll (newPoll) {
  return dispatch => {
    axios.post('/api/polls', newPoll)
      .then(res => {
        console.log('newPoll submitted successfully!', res.data.poll._id)
        dispatch(resetNewPoll())
        dispatch(addFlashMessage({ type: 'success', text: 'Poll saved!' }))
        dispatch(pollSaved(res.data.poll._id))
      })
      .catch(err => {
        console.error('ERROR: redux: newPoll could not be saved', err)
        dispatch(addFlashMessage({ type: 'error', text: 'Something went wrong. Poll coudn\'t be saved.' }))
      })
  }
}

// ******* Reducers *******

const setNewPollTitleReducer = (state, action) => {
  if (typeof action.value !== 'string') {
    console.error('ERROR: redux: setNewPollTitle wasn\'t passed a string:', action.value)
    return Object.assign({}, state)
  }
  return Object.assign({}, state, { newPollTitle: action.value })
}
const setTitleEditableReducer = (state, action) => {
  if (typeof action.value !== 'boolean') {
    console.error('ERROR: redux: setTitleEditable was not passed a boolean:', action.value)
    return Object.assign({}, state, { titleEditable: true })
  }
  return Object.assign({}, state, { titleEditable: action.value })
}
const updateOptionReducer = (state, action) => {
  if (action.value.length < 2) {
    console.error('ERROR: redux: less than two options were passed to updateOption:', action.value)
    return Object.assign({}, state)
  }
  return Object.assign({}, state, { newPollOptions: action.value })
}
const resetNewPollReducer = (state, action) => {
  const newState = {}
  const blankPollState = DEFAULT_STATE
  Object.assign(newState, state, blankPollState)
  return newState
}
const pollSavedReducer = (state, action) => {
  if (typeof action.pollId !== 'string') {
    console.error('ERROR: redux: pollSaved was not passed an id as a string:', action.pollId)
    return Object.assign({}, state, { pollSaved: false })
  }
  return Object.assign({}, state, { pollSaved: action.pollId })
}
const resetPollSavedReducer = (state, action) => {
  return Object.assign({}, state, { pollSaved: null })
}

// ******* Root Reducer Slice *******
export default function newPoll (state = DEFAULT_STATE, action) {
  switch (action.type) {
    case SET_NEW_POLL_TITLE:
      return setNewPollTitleReducer(state, action)
    case SET_NEW_TITLE_EDITABLE:
      return setTitleEditableReducer(state, action)
    case UPDATE_OPTION:
      return updateOptionReducer(state, action)
    case RESET_NEW_POLL:
      return resetNewPollReducer(state, action)
    case POLL_SAVED:
      return pollSavedReducer(state, action)
    case RESET_POLL_SAVED:
      return resetPollSavedReducer(state, action)
    default:
      return state
  }
}

{% endhighlight %}

There is a lot going in this file, but it progresses logically. For a `newPoll`, the actions, action creators, reducer functions, and the rootReducerSlice are all right here, and not in separate files. Adding a new feature is as easy as adding the relevant code to each section, instead of bouncing back and forth between files. It's easier to reason about as well (for me at least).

One way to make this cleaner would be to group the Action Types, Actions, and Reducers together for each feature, and maybe even put each feature into separate files to be imported into this current one for the Root Reducer Slice to use.

Here's how grouping just the action creators and reducers together can look:

{% highlight javascript %}
import axios from 'axios'
import { addFlashMessage } from './flashMessage'

export const DEFAULT_STATE = {
  newPollTitle: '',
  titleEditable: true,
  newPollOptions: [
    '',
    ''
  ],
  pollSaved: null
}

// ******* Action Types *******

const SET_NEW_POLL_TITLE = 'SET_NEW_POLL_TITLE'
const SET_NEW_TITLE_EDITABLE = 'SET_NEW_TITLE_EDITABLE'
const UPDATE_OPTION = 'UPDATE_OPTION'
const RESET_NEW_POLL = 'RESET_NEW_POLL'
const POLL_SAVED = 'POLL_SAVED'
const RESET_POLL_SAVED = 'RESET_POLL_SAVED'

// ******* Action Creators & Reducers*******

/**
 * Sets state.newPollTitle
 *
 * @param {string} pollTitle
 */
export function setNewPollTitle (pollTitle) {
  return { type: SET_NEW_POLL_TITLE, value: pollTitle }
}
const setNewPollTitleReducer = (state, action) => {
  if (typeof action.value !== 'string') {
    console.error('ERROR: redux: setNewPollTitle wasn\'t passed a string:', action.value)
    return Object.assign({}, state)
  }
  return Object.assign({}, state, { newPollTitle: action.value })
}
/**
 * Sets state.titleEditable
 *
 * @param {boolean} bool
 */
export function setTitleEditable (bool) {
  return { type: SET_NEW_TITLE_EDITABLE, value: bool }
}
const setTitleEditableReducer = (state, action) => {
  if (typeof action.value !== 'boolean') {
    console.error('ERROR: redux: setTitleEditable was not passed a boolean:', action.value)
    return Object.assign({}, state, { titleEditable: true })
  }
  return Object.assign({}, state, { titleEditable: action.value })
}
/**
 * Sets state.newPollOptions
 *
 * @param {array} updatedOptions - An array of at least 2 strings
 */
export function updateOption (updatedOptions) {
  return { type: UPDATE_OPTION, value: updatedOptions }
}
const updateOptionReducer = (state, action) => {
  if (action.value.length < 2) {
    console.error('ERROR: redux: less than two options were passed to updateOption:', action.value)
    return Object.assign({}, state)
  }
  return Object.assign({}, state, { newPollOptions: action.value })
}
/**
 * Resets state to DEFAULT_STATE
 */
export function resetNewPoll () {
  return { type: RESET_NEW_POLL }
}
const resetNewPollReducer = (state, action) => {
  const newState = {}
  const blankPollState = DEFAULT_STATE
  Object.assign(newState, state, blankPollState)
  return newState
}
/**
 * Sets state.pollSaved as a new Poll's ID
 *
 * @param {string} pollId - A new poll's mongoDB _id
 */
export function pollSaved (pollId) {
  return { type: POLL_SAVED, pollId }
}
const pollSavedReducer = (state, action) => {
  if (typeof action.pollId !== 'string') {
    console.error('ERROR: redux: pollSaved was not passed an id as a string:', action.pollId)
    return Object.assign({}, state, { pollSaved: false })
  }
  return Object.assign({}, state, { pollSaved: action.pollId })
}
/**
 * Sets state.pollSaved as null
 */
export function resetPollSaved () {
  return { type: RESET_POLL_SAVED }
}
const resetPollSavedReducer = (state, action) => {
  return Object.assign({}, state, { pollSaved: null })
}
/**
 * Submits a new poll to the server
 *
 * @param {object} newPoll - an object containing a new poll's title, at least two
 * options, and the owner (user). Sample: { title: 'What number?', options: ['one', 'two'], owner: 'Lloyd'}
 *
 */
export function submitNewPoll (newPoll) {
  return dispatch => {
    axios.post('/api/polls', newPoll)
      .then(res => {
        console.log('newPoll submitted successfully!', res.data.poll._id)
        dispatch(resetNewPoll())
        dispatch(addFlashMessage({ type: 'success', text: 'Poll saved!' }))
        dispatch(pollSaved(res.data.poll._id))
      })
      .catch(err => {
        console.error('ERROR: redux: newPoll could not be saved', err)
        dispatch(addFlashMessage({ type: 'error', text: 'Something went wrong. Poll coudn\'t be saved.' }))
      })
  }
}

// ******* Root Reducer Slice *******

export default function newPoll (state = DEFAULT_STATE, action) {
  switch (action.type) {
    case SET_NEW_POLL_TITLE:
      return setNewPollTitleReducer(state, action)
    case SET_NEW_TITLE_EDITABLE:
      return setTitleEditableReducer(state, action)
    case UPDATE_OPTION:
      return updateOptionReducer(state, action)
    case RESET_NEW_POLL:
      return resetNewPollReducer(state, action)
    case POLL_SAVED:
      return pollSavedReducer(state, action)
    case RESET_POLL_SAVED:
      return resetPollSavedReducer(state, action)
    default:
      return state
  }
}

{% endhighlight %}

The file is still pretty long, but this refactor is already much easier to reason about. Keeping Each action creator and its corresponding reducer together eliminate the need to scroll back and forth while changing things, or adding a new action/reducer flow.

### Thunks Middleware

Out of the box, Redux only supports a strictly synchronous [data flow](http://redux.js.org/docs/basics/DataFlow.html).

Thankfully, middleware make it easy to dispatch functions and promises, instead of synchronous action creators alone. This allows you to perform operations after a action is dispatched, but before the reducer is called.

[`redux-thunk`](https://github.com/gaearon/redux-thunk) for example, will hijack a dispatched action when a function is returned and tell Redux "Hey, I've got this. Do other things, and I'll get back to you." Once an async operation, like commonly a promise, gets resolved or rejected, you can dispatch any actions you'd like depending on result of the promise, and Redux will handle those resulting action dispatches. The final actions must return normal action objects with at least a `type` property.

You can uses a thunk for dispatching actions conditionally as well. A thunk function is simply a wrapper function used to delay the evaluation inside of it.

You can dispatch more than one action in a `.then` or `.catch` callback after a promise is resolved or rejected, but the final actions dispatched must return normal action objects, each with at least a `type` property. The module's root reducer will use the `action.type` property of the dispatched action in its switch statement to determine which reducer to run (if any).

Handling async actions in Redux is simple using thunks.

Note the `axios` call from the example above:

{% highlight javascript %}
/**
 * Submits a new poll to the server
 *
 * @param {object} newPoll - an object containing a new poll's title, at least two
 * options, and the owner (user). Sample: { title: 'What number?', options: ['one', 'two'], owner: 'Lloyd'}
 *
 */
export function submitNewPoll (newPoll) {
  return dispatch => {

    // You can dispatch a "loading" action here

    axios.post('/api/polls', newPoll)
      .then(res => {
        console.log('newPoll submitted successfully!', res.data.poll._id)
        dispatch(resetNewPoll())
        dispatch(addFlashMessage({ type: 'success', text: 'Poll saved!' }))
        dispatch(pollSaved(res.data.poll._id))

        // dispatch loading done!
      })
      .catch(err => {
        console.error('ERROR: redux: newPoll could not be saved', err)
        dispatch(addFlashMessage({ type: 'error', text: 'Something went wrong. Poll coudn\'t be saved.' }))

        // dispatch loading done!
      })
  }
}
{% endhighlight %}

After getting a response from the API, a certain set of action creators will get dispatched if the response is successful and the promise is resolved, and other action creators will get dispatched if the response is an error and the promise gets rejected.

Writing a thunk action involves simply returning a function taking in `dispatch` as its parameter, and making your async request (a side effect) within the function.

Action creators in Redux are meant to be pure functions by default, but thunks allow you to make them "impure" and handle side effects effectively.

[redux-saga](https://github.com/redux-saga/redux-saga) may be worth looking into as well if you have some complex async logic to handle. Sagas allow [cancellation](https://redux-saga.github.io/redux-saga/docs/advanced/TaskCancellation.html) and ["concurrency"](https://redux-saga.github.io/redux-saga/docs/advanced/Concurrency.html) in your async operations using generators.

Note that ["concurrency" in JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop) as its used redux-saga involves an event loop on a single thread, and not multiple processes on individual threads like in other languages.

With web workers however, you can now take advantage of multi-threading with JavaScript. Check out [`Hampsters.js]`(https://github.com/austinksmith/Hamsters.js) for that awesomeness. It's best not to go that route until you absolutely need to.

## Unit Testing

A lot of wins are gained with unit tests. They force you to think about common edge cases in your business logic and how to handle them, they act as documentation for how your logic works in detail, and they encourage simple, decoupled code. If your code is hard to test, that's a code smell. As you develop your app, you're probably going to break things you've already worked on as you add more features. Well thought out unit tests will let you know right away when something breaks so you don't end up with a nasty surprise later on. Just having a test runner touch a good majority of your code frequently is a good sanity check, and it builds confidence in your codebase as it grows even if a lot of people are working on it at the same time.

There are many great testing libraries and frameworks out there to choose from. I've been using [Jest](https://facebook.github.io/jest/) lately, but [Mocha](https://mochajs.org/) and [Tape](https://github.com/substack/tape) are very popular and current as well. Any of them are fine.

### Testing React with Jest and Enzyme

Jest is well suited for React. You can write [snapshot](https://facebook.github.io/jest/docs/snapshot-testing.html#content) tests of your components, which render components into markup as JSON using whatever props you'd like. The test fails if the markup changes, and you'll see a diff of what changed similar to a Git diff. You can update the snapshot by running `jest --updateSnapshot` or just `jest -u`. Snapshots are far from bulletproof, but they're cheap and disposable, and will ensure that your markup is rendering as expected.

For simplicity, let's go back to `[SpiffyWikipedia`](https://github.com/itxchy/FCC-spiffy-wikipedia/blob/master/src/components/SearchResults/SearchResults.test.js) for a snapshot example:

{% highlight javascript %}
import React from 'react'
import { shallow } from 'enzyme'
import { shallowToJson } from 'enzyme-to-json'
import SearchResults from './SearchResults'

test('SearchResults snapshot test', () => {
  const component = shallow(<SearchResults error={null} searchResults={[]} />)
  const tree = shallowToJson(component)
  expect(tree).toMatchSnapshot()
})

{% endhighlight %}

We'll get to `enzyme` in a moment.

`shallow` renders the component, `shallowToJson` creates a JSON tree from `shallow`'s render, and you just need to `expect` the tree `.toMatchSnapshot`. `expect` comes with Jest for free.

If no snapshot file exists, Jest will create one for you. A directory will appear called `__snapshots__` in same directory as your test file.

Beyond snapshots, [Enzyme](http://airbnb.io/enzyme/) from AirBnB is essential for testing your components more deeply. It works great with Jest, but it's also commonly used with Mocha.

Enzyme has few different rendering functions for your components like [shallow](http://airbnb.io/enzyme/docs/api/shallow.html) and [mount](http://airbnb.io/enzyme/docs/api/shallow.html).

Shallow only renders a component's surface-level markup, and none of its child components it may have. Shallow doesn't render a full DOM, so you won't have access to DOM API's or the component's lifecycle methods. The trade-off is that Shallow is fast. It's great for testing if a component's markup is showing up properly with more detail than snapshots. Shallow should be used as much as possible before using `mount`.

Here are a few `shallow` tests from `SpiffyWikipedia`:

{% highlight javascript %}
import React from 'react'
import { shallow } from 'enzyme'
import { shallowToJson } from 'enzyme-to-json'
import testData from '../../testData.json'
import SearchResults from './SearchResults'
import Result from '../Result/Result'

test('SearchResults snapshot test', () => {
  const component = shallow(<SearchResults error={null} searchResults={[]} />)
  const tree = shallowToJson(component)
  expect(tree).toMatchSnapshot()
})

test('SearchResults should render a Results component for each Result', () => {
  const component = shallow(<SearchResults error={null} searchResults={testData} />)
  expect(component.find(Result).length).toEqual(testData[1].length)
})

test('SearchResults should render "Nothing Found" if there are no results', () => {
  const component = shallow(<SearchResults error={null} searchResults={["nothing", [], [], [], []]} />)
  expect(component.text()).toEqual('Nothing Found')
})

test('SearchResults should render "An error occurred." if an error is passed to props', () => {
  const component = shallow(<SearchResults error={ { error: 'bad news' } } searchResults={null} />)
  expect(component.text()).toEqual('An error occurred.')
})

test('SearchResults should render "An error occurred." if nothing is is passed to seachResults or error as props', () => {
  const component = shallow(<SearchResults error={null} searchResults={null} />)
  expect(component.text()).toEqual('An error occurred.')
})
{% endhighlight %}

These tests expect the correct markup and components to appear depending on what prop values are passed into `<SearchResults />`. Shallow has a lot of methods you can use to target markup inside your component, and the fine folks at AirBnB offer some great [documentation](http://airbnb.io/enzyme/docs/api/shallow.html).

[`SearchBar`](https://github.com/itxchy/FCC-spiffy-wikipedia/blob/master/src/components/SearchBar/SearchBar.js) was a bit more complicated to test. Some DOM events needed to be simulated, so `mount` was used instead of `shallow`:

{% highlight javascript %}
import React from 'react'
import { shallow, mount } from 'enzyme'
import { shallowToJson } from 'enzyme-to-json'
import SearchBar from './SearchBar'

test('SearchBar snapshot test', () => {
  const submitHandler = jest.fn()
  const component = shallow(<SearchBar onSearchSubmit={submitHandler} />)
  const tree = shallowToJson(component)
  expect(tree).toMatchSnapshot()
})

test('SeachBar should change state.searchText on every keystroke', () => {
  const submitHandler = jest.fn()
  const component = mount(<SearchBar onSearchSubmit={submitHandler} />)
  component.find('.search-input').simulate('change', { target: { value: 'search term'} })
  expect(component.state('searchText')).toEqual('search term')
})

test('SearchBar should call onSearchSubmit from props on populated form submit', () => {
  const submitHandler = jest.fn()
  const component = mount(<SearchBar onSearchSubmit={submitHandler} />)
  component.setState({ searchText: 'Prince' })
  component.find('#search-submit').simulate('click')
  expect(submitHandler).toHaveBeenCalledWith('Prince')
})

test('SearchBar should not call onSearchSubmit from props if input is empty', () => {
  const submitHandler = jest.fn()
  const component = mount(<SearchBar onSearchSubmit={submitHandler} />)
  component.find('#search-submit').simulate('click')
  expect(submitHandler).not.toHaveBeenCalled()
})

{% endhighlight %}

Mount renders a full DOM, so you're now able to interact with DOM API's and component lifecycle methods.

In the tests above, form change events and button click events are simulated to test how the component behaves.

This is about as deep as I've gotten with testing React components. You can test all sorts of aspects of their behavior, but I think it's best to let your React components focus mainly on presentation, and limit business logic as much as possible. If your components have a lot of complex operations going on, you can easily abstract that logic away from React and into small external modules that are much easier to kick around with unit tests. By the time your app needs Redux, most of your business logic will be handled by your action creators and reducers, which are very easy to test since they're just JavaScript.

### Testing Redux with Jest

Testing Redux is as simple as calling a reducer with a default state object and an action containing dummy data, and verifying the returned new state object against what you're expecting. Let's look at few examples from Vote.

Here are a few action creators alongside their reducers in [createNewPoll.js](https://github.com/itxchy/FCC-vote/blob/master/redux/modules/createNewPoll.js):

{% highlight javascript %}
// ...

/**
 * Sets state.newPollOptions
 *
 * @param {array} updatedOptions - An array of at least 2 strings
 */
export function updateOption (updatedOptions) {
  return { type: UPDATE_OPTION, value: updatedOptions }
}
const updateOptionReducer = (state, action) => {
  if (action.value.length < 2) {
    console.error('ERROR: redux: less than two options were passed to updateOption:', action.value)
    return Object.assign({}, state)
  }
  return Object.assign({}, state, { newPollOptions: action.value })
}
/**
 * Resets state to DEFAULT_STATE
 */
export function resetNewPoll () {
  return { type: RESET_NEW_POLL }
}
const resetNewPollReducer = (state, action) => {
  const newState = {}
  Object.assign(newState, state, DEFAULT_STATE)
  return newState
}
/**
 * Sets state.pollSaved as a new Poll's ID
 *
 * @param {string} pollId - A new poll's mongoDB _id
 */
export function pollSaved (pollId) {
  return { type: POLL_SAVED, pollId }
}
const pollSavedReducer = (state, action) => {
  if (typeof action.pollId !== 'string') {
    console.error('ERROR: redux: pollSaved was not passed an id as a string:', action.pollId)
    return Object.assign({}, state, { pollSaved: false })
  }
  return Object.assign({}, state, { pollSaved: action.pollId })
}

// ...
{% endhighlight %}

And here are their tests in [createNewPoll.spec.js](https://github.com/itxchy/FCC-vote/blob/master/redux/modules/createNewPoll.spec.js):

{% highlight javascript %}
// ...

  describe('updateOption', () => {
    it('should set state.newPollOptions as the array of option strings passed to it', () => {
      let state = newPollReducerSlice(null, updateOption(['thing 1', 'thing 2']))
      expect(state.newPollOptions).toEqual(expect.arrayContaining(['thing 1', 'thing 2']))
    })
    it('should return previous state if less than two option strings are given', () => {
      let state = Object.assign({}, DEFAULT_STATE, { newPollOptions: ['thing 1', 'thing 2']})
      let newState = newPollReducerSlice(state, updateOption(['thing 3']))
      expect(newState.newPollOptions).toEqual(expect.arrayContaining(['thing 1', 'thing 2']))
    })
  })

  describe('resetNewPoll', () => {
    it('should return the createNewPoll module to its default state', () => {
      let state = Object.assign({}, DEFAULT_STATE, { newPollTitle: 'Things' })
      expect(state.newPollTitle).toBe('Things')
      let newState = newPollReducerSlice(state, resetNewPoll())
      expect(newState).toEqual(DEFAULT_STATE)
    })
  })

  describe('pollSaved', () => {
    it('should set state.pollSaved as a string of a new poll\'s id', () => {
      let state = newPollReducerSlice(null, pollSaved('asdfASDF'))
      expect(state.pollSaved).toBe('asdfASDF')
    })
    it('should set state.pollSaved as false if a string is not passed to pollSaved', () => {
      let state = newPollReducerSlice(null, pollSaved(undefined))
      expect(state.pollSaved).toBe(false)
    })
  })

// ... /
{% endhighlight %}

In each test, `createNewPoll.js`'s root reducer slice is returning a new state object based on the previous or default state, and an action with test data. If the new state object returned from the reducer is what you expect, the test passes. If not, you know exactly where to look for the bug.

> A root reducer slice is simply the module's exported reducer that gets passed to combineReducer in the rootReducer.js file. It's a bit confusing to think about at first. Put another way, the root reducer is Redux's main reducer that reduces everything into its one state object. The root reducer is comprised of a number of root reducer slices, each being an individual modules main reducer. A module's main reducer (or root reducer slice) will call one of the many individual reducers in that module if an action's type gets matched. That reducer will return a new state, and it will be passed back to combineReducers into Redux's main root reducer, and Redux's store will have a brand new state object.

A lot more edge cases need to be considered for these tests like wrong types out-of-range values, super long strings, etc. being passed as parameters to action creators, but this is a good start. In production apps with real clients, thousands of users will use and abuse your forms every day. Applications need to be hardened against bad data. This is important for security too, but more on that later.

## Styling

`webpack` makes it easy to modularize your CSS and import individual CSS modules into your React components. `create-react-app` encourages this by default, and I really enjoyed this workflow in my [Spiffy Wikipeda](https://github.com/itxchy/FCC-spiffy-wikipedia/blob/master/src/components/Result/Result.css) app. Maintaining CSS styles is getting easier all the time. [`PostCSS`](https://github.com/postcss/postcss) offers many powerful plugins as well, which will work with your favorite CSS preprocessor.

For vote, I'll admit I didn't focus as much attention on styling as I should have. For simplicity, I elected to keep my SASS and CSS considerations separate from my React code. I used `webpack`'s `ExtractTextPlugin` to bundle all of the compiled CSS into a separate css file, and import the `main.scss` directly into [`BrowserEntry.js`](https://github.com/itxchy/FCC-vote/blob/master/components/BrowserEntry.jsx#L4) so Webpack would know about it.

All of my SCSS files were stored in a separate `sass` directory, the idea being to keep styling concerns and React component concerns separate. But isn't the *styling* of a component part of a component's concerns? Depends on who you ask I guess, but importing your SASS or CSS modules directly into the components that need them makes a lot of sense. Instead of going through a separate directory tree looking for the style module that's responsible for your component, and managing the file structure of a separate style directory, it makes sense to keep your CSS or SASS together with the components they're styling. This is good for a faster workflow, and it makes it easier to use CSS conventions like [BEM](https://en.bem.info/methodology/key-concepts/). Instead of mirroring your CSS directory with your component directory, you can keep your components even more self contained in their file structure.

![Styling in its own directory compared with keeping styles with their components.]({{ site.url }}/public/img/css-structure.png)

*How* to [bundle your CSS](https://webpack.js.org/guides/code-splitting-css/) is another consideration. Using `ExtractTextPlugin` will allow you to load your CSS in a separate script tag, requiring an additional network request. Including your CSS in a JavaScript bundle will save a network request, but will add to the bundle's weight.

# The Server

Vote uses Express to handle page requests, as well as API requests for CRUD operations with MongoDB.

## Server Side Rendering

Vote's React markup is rendered on the [server](https://github.com/itxchy/FCC-vote/blob/master/server.js) into a string using `ReactDOMServer.renderToString`, and passed down to the client. By the time the client bootstraps the JavaScript bundles, the client-side application code from the bundle takes over user interactions. This is great for a few reasons. First, client-side React doesn't need to re-render the page's markup when it finally bootstraps in the browser. Second, this allows the server to respond with markup as if it were static (it's made into static markup on the fly), which is great for users with slow connections. A lot of people are stuck with 2G around the world. Instead of having to wait many seconds for a bundle to download before seeing anything, the pre-rendered markup will show up almost immediately. Once the app's bundle finishes downloading, the app will be interactive. This is great for SEO, since the pre-rendered markup is crawlable for search engine bots.

The code itself is very simple. Thanks again to Brain Holt for teaching this pattern in the first Complete Intro To React!

{% highlight javascript %}
app.use((req, res) => {
  match({ routes: Routes(), location: req.url }, (error, redirectLocation, renderProps) => {

    if (error) {
      res.status(500).send(error.message)

    } else if (redirectLocation) {
      res.redirect(302, redirectLocation.pathname + redirectLocation.search)

    } else if (renderProps) {
      const body = ReactDOMServer.renderToString(
        React.createElement(
          Provider, { store }, React.createElement(RouterContext, renderProps)
        )
      )
      res.status(200).send(template({ body }))
    } else {
      res.status(404).send('404: Page Not Found')
    }
  })
})
{% endhighlight %}

> Note: that this code works for React Router 2. The [documentation](https://github.com/ReactTraining/react-router/blob/master/docs/guides/ServerRendering.md) for server-side rendering doesn't mention any changes of this pattern from v2 to v3 , but you've been warned.
>
> React Router 4 is looking amazing and you should probably just make the jump if you're starting a new project. The final version will be released very soon, and the [documentation](https://reacttraining.com/react-router/) got a complete revamp. You can use [`<StaticRouter>`](https://reacttraining.com/react-router/#staticrouter) and [`match`](https://reacttraining.com/react-router/#match) for server-side rendering if you're using version 4. Brian Holt explains how to tie them together in his second Complete Intro to React course on Front End Masters. It's worth it! React Router has had a lot of API churn over the past few years, but it the twitter whispers are reliable, version 4 will be the last major API redesign for the foreseeable future. I'm glad Ryan Florence and Michael Jackson elected to take the heat from disgruntled users while working tirelessly to build a more finalized router API that will be much better for the long term. The breaking changes were well worth it to get to this point.

The code above seems pretty busy at first glance, but let's break this down.

{% highlight javascript %}
app.use((req, res) => {

  match({ routes: Routes(), location: req.url }, (error, redirectLocation, renderProps) => {
    // handle the result
  })

})
{% endhighlight %}

When a request is received by Express, the `match` function from `react-router` is called. It takes two parameters, the first being an object where it can learn about your application's `routes` (returned from a function in this case), and a `location` (the request's URL) to match to the routes. It can also take in a `history` property if you'd like.

One aside: in your `index.html` file, make sure you include a forward slash in your bundle script locations or you might get basename errors when reloading nested routes, or navigating to nested routes directly:

{% highlight html %}
    <script src="/public/vendor.js" charset="utf-8"></script>
    <script src="/public/bundle.js" charset="utf-8"></script>
{% endhighlight %}

`match`'s second argument is a callback with three parameters, `error`, `redirectLocation`, and `renderProps`.

The meat of that callback simply handles an `error` if it is defined, a `redirectLocation` if it is defined, `renderProps` if it is defined (this is how you know there is a successful match between the requested `location` and a known route), and finally falls back on a 404 error if none of the callback's parameters are defined. If there is no error, no renderProps, and no redirect, then there is no match.

{% highlight javascript %}
app.use((req, res) => {

  match({ routes: Routes(), location: req.url }, (error, redirectLocation, renderProps) => {
    if (error) {

      res.status(500).send(error.message) // 500, something went wrong. You'll want to send a calming 500.html page instead of the error message itself

    } else if (redirectLocation) {

      res.redirect(302, redirectLocation.pathname + redirectLocation.search) // 302 redirect. redirectLocation is a LocationDescriptor object that tells you where to go next.

    } else if (renderProps) {

      const body = ReactDOMServer.renderToString(
        React.createElement(
          Provider, { store }, React.createElement(RouterContext, renderProps)
        )
      ) // Render the things.

      res.status(200).send(template({ body })) // 200 success! A match exists, inject the markup into <%= body %> in index.html and send it out.
    } else {
      res.status(404).send('404: Page Not Found') // No match, must be a 404.
    }
  })
})
{% endhighlight %}

> More about [`LocationDescriptor`](https://github.com/ReactTraining/react-router/blob/master/docs/Glossary.md#locationdescriptor)

The actual rendering happens thanks to `ReactDOMServer.renderToString`. Since this is happening on the server, using JSX would add necessary complexity since JSX needs to be transpiled. Instead, using `React.createElement` for the two elements we need works out fine without too much nesting.

#### `React.createElement` Refresher

JSX is syntax to make it easier to compose components. Components are just functions, so we can easily write our components without JSX. [`React.createElement`](https://facebook.github.io/react/docs/react-without-jsx.html) takes in three parameters: `component`, `props`, and `children`.

{% highlight javascript %}
const body = ReactDOMServer.renderToString(
  React.createElement(
    Provider, { store }, React.createElement(RouterContext, renderProps)
  )
)
{% endhighlight %}

In JSX, this would translate to:

{% highlight javascript %}
const body = (
  <Provider store={store}>
    <RouterContext renderProps={renderProps} />
  </Provider>
)
{% endhighlight %}

Those two elements will render into the entire page's markup.

### Pre-Transpiling For Node Using Babel

One obstacle to rendering a React application on the server is that Node doesn't understand JSX or ES6 module syntax, not to mention any other ES2016-ES2017+ syntax we might have leaned on Babel to transpile in the application code.

There are a few options to transpile the application code for the server.

Brian Holt teaches with `babel-register`, which can be required into your server code.

{% highlight javascript %}
require('babel-register')({ ignore: /node_modules/ } )
{% endhighlight %}

> I ended up adding an option to ignore `node_modules` because I think there was a `.babelrc` file somewhere in a 3rd party module that was overriding my own.

This works great for development, but you don't want to use this in [production](https://github.com/thejameskyle/babel-handbook/blob/master/translations/en/user-handbook.md#babel-register).

For server-side code, it's better to transpile ahead of time, instead of `babel` Transpiling the same code over and over again on the server.

To pull this off, I made a `production` directory, and set up some `npm` scripts to transpile a copy of all of the application code that the server needs to render, and then put that process into the production build step.

{% highlight javascript %}
  "scripts": {
    "build:prod": "npm run babel:prod && webpack --config webpack.prod.js",
    "babel:clear": "del production/*",
    "babel:components": "NODE_ENV=server babel components -d production/components",
    "babel:redux": "NODE_ENV=server babel redux -d production/redux",
    "babel:auth": " NODE_ENV=server babel auth -d production/auth",
    "copy:sass": "mkdir production/sass && cp -r sass/* production/sass",
    "babel:routes": "NODE_ENV=server babel routes -d production/routes",
    "babel:prod": "npm run babel:clear && npm run copy:sass && npm run babel:routes && npm run babel:components && npm run babel:redux && npm run babel:auth",
    ...
  }
{% endhighlight %}

Now, when running `build:prod`, all of my application files will be transpiled, and the `sass` folder will be copied to the production directory too so that the application doesn't panic (gross, I know). I'm a a new convert for keeping styles with their components in the same directory to begin with in React apps.

That's great, but there are a few more steps.

It would be a drag to have to transpile everything every time a file changes during development, so it would be nice to still be able to use `babel-register` outside of a 'production' environment.

{% highlight javascript %}
if (process.env.NODE_ENV !== 'production') {
  require('babel-register')({ ignore: /node_modules/ })
}
{% endhighlight %}

Finally, the correct application files need to be required based on the environment:

{% highlight javascript %}
const { store } = process.env.NODE_ENV === 'production' ? require('./production/redux/Store.js') : require('./redux/Store.js')

const { Routes } = process.env.NODE_ENV === 'production' ? require('./production/components/Routes.js') : require('./components/Routes.jsx')
{% endhighlight %}

Now it's closer to production ready.

When the app is in a production environment, `babel-register` won't be run, and React will render the pre-transpiled version of the app. During development, `babel-register` will transpile on the fly, and ReactDOMServer can point to our actual application code without a lengthy transpilation step before-hand.

## Express API Routes

Vote's API was meant to be [RESTful](http://www.restapitutorial.com/lessons/whatisrest.html).

### RESTful Dialog Between Machines

REST is more of a paradigm than a standard that describes an interface between a client and a server that is uniform, stateless, and explicit. Resources are passed between computers (usually in JSON, but XML is still prevalent) in a predictable way, so that the two distinct applications don't have to care about what languages, libraries, or even hardware each are using. REST is method of communication with its own customs and dialect.

REST stands for REpresentational State Transfer.

### CRUD Logic

Making a polling application was a great exercise for creating a server API since a number of simple Create Read Update Delete operations would be necessary.

From [`polls.js`](https://github.com/itxchy/FCC-vote/blob/master/routes/polls.js)
{% highlight javascript %}
/**
 * Retrieves all polls
 */
router.get('/', (req, res) => {
  Poll.find()
    .select('_id title options totalVotes owner')
    .exec()
    .then(polls => {
      return res.json(polls)
    })
    .catch(err => res.status(500).json({ 'error retrieving all polls': err }))
})

/**
 * Retrieves all of a user's polls
 */
router.get('/:user', (req, res) => {
  Poll.find({ owner: req.params.user })
    .select('_id title options totalVotes owner')
    .exec()
    .then(polls => {
      return res.json(polls)
    })
    .catch(err => res.status(500).json({ 'error retrieving current user\'s polls': err }))
})

/**
 * Retrieves a single poll based on poll ID
 */
router.get('/id/:id', (req, res) => {
  Poll.find({ _id: req.params.id })
    .select('_id title options totalVotes owner')
    .exec()
    .then(poll => {
      console.log('mongo returned this poll:', poll)
      return res.json(poll)
    })
    .catch(err => res.status(500).json({ 'error retrieveing single poll': err }))
})

/**
 * Deletes a poll forever based on ID
 */
router.delete('/delete/:id', (req, res) => {
  Poll.find({ _id: req.params.id })
    .remove()
    .exec()
    .then(poll => {
      console.log('This poll has been deleted!:', poll)
      return res.json(poll)
    })
    .catch(err => res.status(500).json({'error deleting poll': err}))
})

{% endhighlight %}

This application uses Express Router, so picture these endpoints as prepended with `/api/polls`.

Anytime a request reaches the server, it first passes through Express' middleware, which includes express router. Here's a heavily redacted [`server.js`](https://github.com/itxchy/FCC-vote/blob/master/server.js):

{% highlight javascript %}
const express = require('express')
const users = require('./routes/users.js')
const auth = require('./routes/auth.js')
const polls = require('./routes/polls.js')
const app = express()

app.use(bodyParser.json())

/**
 * Routes
 */
app.use('/api/users', users)
app.use('/api/auth', auth)
app.use('/api/polls', polls)

app.use('/public', expressStaticGzip('./public'))
{% endhighlight %}

One mistake I made while building this API was that I didn't design a blueprint first.

It's a good idea to have all of your endpoints and data schemas mapped out beforehand so you don't end up having to change it often as you build out features.

In the example above from [`polls.js`](https://github.com/itxchy/FCC-vote/blob/master/routes/polls.js), those endpoints could be crudely mapped out this way:

{% highlight markdown %}
GET: /api/polls
  - responds with all polls

GET: /api/polls/:username
  - responds with all of a user's polls

GET: /api/polls/id/:id
  - responds with a single poll based on its ID

DELETE: /api/polls/delete/:id
  - deletes a poll based on its ID, and responds with the just-deleted poll object
{% endhighlight %}

As I developed this application, its easy to see that I built the first two endpoint before the last two. It makes sense to think of endpoints as if you're accessing a directory structure. In this case, the username endpoint isn't very descriptive. `/api/polls/username/:username` would be more clear.

The DELETE endpoint could have just pointed to `/api/polls/id/:id` as well, but what if someone (possibly me) accidentally set a DELETE request to that URI instead of a GET request to that same URI and it somehow made it to production? Someone could lose priceless data from a groundbreaking poll.

API design is a deep subject and there are a lot different opinions about them, but REST offers some great guidelines to follow.

### Avoiding Callback Hell With Async/Await

Promises work very well by themselves, but I've really enjoyed working with [`async`/`await`](http://www.2ality.com/2016/10/async-function-tips.html) syntax. It allows you to program with asynchronous functions while treating them as if they're synchronous. Generators and Promises are very effective, but I enjoy the simplicity and readability of `async`/`await`. It's really cool that so many great alternatives to callbacks have entered the wild in just a few short years!

#### Not So Fast...

Unfortunately, Node 6 doesn't support `async`/`await`, but [Node 7 does](http://node.green/#ES2017-features-async-functions) and Node 8 is scheduled for release in [April 2017](https://github.com/nodejs/LTS)! By the time you read this, async/await will probably be available natively in Node.

For now, I used a library called `asyncawait` to be able to use async/await as functions so no transpiling would be necessary.

{% highlight javascript %}
const async = require('asyncawait/async')
const awaitFake = require('asyncawait/await')

/**
 *  Edits a poll
 */
router.put('/edit/:id', authenticate, (req, res) => {
  const validate = validateUpdatedPoll(res, req.body, commonValidations)
  if (!validate.isValid) {
    return res.status(400).json({ 'bad request': 'bad data for poll edit', errors: validate.errors })
  }

  const applyPollEdits = async(function applyPollEdits (req, res) {
    try {
      let updatedPoll = awaitFake(updatePollDocumentOnEdit(req.params.id, req.body))
      console.log('edit: updatedPoll:', updatedPoll)
      if (updatedPoll.updatedDoc) {
        return res.json(updatedPoll.updatedDoc)
      }
      if (updatedPoll.error) {
        return res.status(500).json({'poll edit failed in database operation': updatedPoll.error})
      }
    } catch (error) {
      console.error(error)
      return res.status(500).json({'poll edit failed': error})
    }
  })

  applyPollEdits(req, res)
})

{% endhighlight %}

Keep in mind that `async` and `await` are reserved keywords in the spec, and NOT functions like they're shown here. This [2ality article](http://www.2ality.com/2016/10/async-function-tips.html) goes deep into async/await in addition to other asynchronous syntax's coming soon.

The `asyncawait` library behaves just like the real thing, so it's fine until I can use async await natively.

Let's look at an older commit when I was still using `babel-register` on the server for all environments:

{% highlight javascript %}
/**
 *  Edits a poll
 */
router.put('/edit/:id', authenticate, (req, res) => {
  const validate = validateUpdatedPoll(res, req.body, commonValidations)
  if (!validate.isValid) {
    return res.status(400).json({ 'bad request': 'bad data for poll edit', errors: validate.errors })
  }
  const pollID = req.params.id

  applyPollEdits(req, res)

  async function applyPollEdits (req, res) {
    try {

      let updatedPoll = await updatePollDocumentOnEdit(req.params.id, req.body)

      if (updatedPoll.updatedDoc) {
        return res.json(updatedPoll.updatedDoc)
      }

      if (updatedPoll.error) {
        return res.status(500).json('poll edit failed in database operation': updatedPoll.error)
      }

    } catch (error) {
      console.error(error)
      return res.status(500).json({'poll edit failed': error})
    }
  }
})
{% endhighlight %}

Could a promise have been used here? Of course, but this project seemed like a good time to give `async`/`await` a try, and it's very simple to use.

Inside of an async function, you set up `try`/`catch` blocks. Inside the `try` block, you can use `await` to essentially tell the function "hey, I'm pausing this context until I hear back from this promise I'm waiting for." In this code, the expressions following the `await` call won't evaluate until a promise from `updatePollDocumentOnEdit` gets resolved or rejected. Once the `updatePollDocumentOnEdit` promise resolves, `updatedPoll` will receive its new value, and the rest of the function will continue. All of this happens while Node does other things outside of the `async` function's context. If a promise gets rejected, you can handle the error in the `catch` block.

This was a simple example with only one `await` statement, but what if you had to perform three or four async function calls? Instead of nested callbacks, or even nested promises, you can use `await` to tell the `async` function to pause until `await` gets a resolution of a promise. This way you can have multiple variables  with `await` calls that will run in order, each pausing until its following promise gets resolved:

{% highlight javascript %}
async function workout (run, updateJournal, highIntensityCardio) {
  try {
      const averageMileTime = await run({ miles: 2 })
      const journalEntry = await updateJournal(averageMileTime)
      const euphoria = await highIntensityCardio()
      return { euphoria, journalEntry }
  } catch (error) {
    return { error }
  }
}

workout(run, updateJournal, highIntensityCardio)

{% endhighlight %}

Many more use cases can be read about [here](http://www.2ality.com/2016/10/async-function-tips.html).

# The Database: MongoDB

I tried out Posgresql with Vote at first, but switched back to MongoDB mostly because I'm more familiar with Mongo and Mongoose, but also because the data of this app works very well for noSQL objects. Translating the poll schema into SQL tables would have gotten messy very quickly, and I'm not great at SQL scripting. I'm sure there's an elegant way to do it that I'm not aware of, but Postgresql isn't going anywhere. The other key factor was being able to use a hosted MongoDB database on [mLab](https://mlab.com/) for free.

## Schema

Going hand-in-hand with designing an API, another key takeaway I took from Vote was thinking about Schema design.

The Schema for polls went through a few iterations but I ended up with this:

{% highlight javascript %}
const mongoose = require('mongoose')

const votesSchema = new mongoose.Schema({
  voter: String
})

const optionsSchema = new mongoose.Schema({
  option: String,
  votes: [votesSchema]
})

const voteSchema = new mongoose.Schema({
  title: String,
  options: [optionsSchema],
  totalVotes: Number,
  owner: String
}, { timestamps: true })

const Poll = mongoose.model('Polls', voteSchema)

module.exports = Poll
{% endhighlight %}

This could probably be simplified, but having the separate schemas made the structure flat and clean to look at. Each level would be easy to extend as well


## Mongoose

Mongoose allows you to chain commands and use promises to simply MongoDB logic.

{% highlight javascript %}
/**
 * Looks up a user based on username or email
 * If no user is found, the response will be null
 */
router.get('/:identifier', (req, res) => {
  User.find({ $or: [{ email: req.params.identifier }, { username: req.params.identifier }] })
  .select('username email')
  .exec()
  .then(user => {
    console.log('user found!', user)
    if (isEmpty(user)) {
      return res.json({user: null})
    }
    return res.json({user})
  })
  .catch(err => {
    console.error('find user promise rejection')
    res.status(400).json({ 'user lookup error': err, error: err })
  })
})

{% endhighlight %}

This code searches the database for an email or username matching the identifier passed into the login form. The promise gets resolved with a user object (either populated with a found user, or empty), otherwise, the error is passed to the `catch` function.

It's important to handle rejected promises quickly so that you can debug them in the right context. Without the `catch` statement, an error here would be more difficult to trace.

## Development Database vs Production Database

During development, you can spin up a database on your system if MongoDB is installed locally, and connect to it with mongoose.

You'll need to create a `data` folder in your project's root directory and another npm script:

{% highlight json %}
"scripts" {
  "mongo": "mongod --port 27017 --dbpath=./data"
}
{% endhighlight %}

You should see a bunch of logs in your terminal if it's working.

Then, you just connect mongoose to `mongodb://localhost:27017/[databaseName]`

For production, you should use an environment variable for your mLab URI, since it's a bad idea to put security credentials directly into your code, especially if you're committing to GitHub publicly.

You can use a conditional statement to connect the proper database based on your node environment:

{% highlight javascript %}
// server.js /
/**
 * connect to MongoDB
 */
if (process.env.NODE_ENV === 'production') {
  mongoose.connect(process.env.MONGO_URI)
    .then(() => {
      console.log('Connected to Mongoose! production environment')
    })
    .catch(err => {
      console.error('Mongoose production connection failed', err)
    })
} else {
  mongoose.connect('mongodb://localhost:27017/vote')
  .then(() => {
    console.log('Connected to Mongoose! development environment')
  })
  .catch(err => {
    console.error('Mongoose development connection failed:', err)
  })
}

{% endhighlight %}

Promises make life easier.

# Production

It's never been easier to deploy to the cloud. You can spin up a VPS instance in a few minutes at Digital Ocean, Linode, and others, or use a platform like Heroku or Amazon EC2 to handle all of the devops for you.

I think it's valuable to go through the process of configuring your own VPS server at least once, as its a great learning experience to get you thinking about Linux, permissions, security, configuring a server that's not Node, caching, and even some rudimentary performance optimizations that you can keep in mind if you build an app that ends up getting real users in the future. Nginx is very powerful, and can be used to stand in front of your node application to serve cached static content much more efficiently.

If you're serving customers, that's when it's better to use a cloud platform like [AWS](https://aws.amazon.com/ec2/) that has a team of seasoned professionals working full time on maintaining the servers as well as security concerns. Plus, it will make scaling out your app quickly a lot easier when your Hat Review app makes it to the front page of Reddit, crushing your server with requests. You'll pay a premium for the extra pre-configured features, but situations like spikes in traffic (the good kind), DDoS attacks (the bad kind), or even intrusive hacking attempts are commonplace on the modern web. If you're serving customers, you have a responsibly to ensure consistent uptime, and protect customer data. Security needs to be a top concern at the development stage, but more on that later.

Cloud platforms like AWS aren't invincible as a lot people found out recently, but it's important to remember that no matter how reliable a cloud service seems, they are all still just computers being maintained by humans. Mistakes will happen, guaranteed. There will be plenty more outages at all sorts of companies in the future, whether from mistakes in a terminal command, a natural disaster, or just too many redundancies failing at the same time. Anticipating disasters, and even simulating them, will help you come up with an established playbook for what to do when the probable eventually happens. Performing "fire drills" regularly is a good practice for companies because when disasters do happen, you'll be following a familiar plan step-by-step, and not reacting to events while in a heightened emotional state. With all of that preparation, however, things will still happen that are beyond your control. When S3 went down, it was probably cheaper for a lot of companies to just accept a few hours of downtime and lost revenue rather than reach for their "solar storm" contingency, which could have taken many more hours to conduct properly than the time of the outage itself. It probably helped that a large chunk of the Internet was in the same boat.

## Continuous Integration

Continuous integration services will build your project in the cloud, and run all of your tests and linting to determine whether your app's build is "passing" or "failing". This ensures that your app can actually install in a variety of environments, and will help you catch resolve dependencies you may have missed in a project if you used a module that was installed globally on your system. This also keeps you from automatically deploying a build with failing tests. With teams of developers, this adds an automated safety check beyond running unit test suites manually.

For Vote, I used Circle CI since it's very easy to use, but I'd recommend Travis CI since it offers a lot more features for free, and their mission is to forever support open source software. Travis CI is heavily integrated with GitHub, and allows maintainers to manage builds from pull requests easily. The [docs](https://docs.travis-ci.com/user/deployment) are very good too.

If you're using a cloud platform like Heroku, you can deploy to a staging server on every successful build. Travis CI has a [guide for that](https://docs.travis-ci.com/user/deployment/heroku/).

### Staging vs Production

It's a good idea to have your automatic deployments first go to a staging server, and then deploy to production manually. The manual production deployment step can be as easy as a single click. This ensures that if a serious mistake passes Travis CI's build step and testing, you can still catch it before passing it on to the production server.

A feature like Heroku [pipelines](https://devcenter.heroku.com/articles/pipelines) is useful for managing multiple deployments that share the same codebase, and you can deploy code from a staging server to a production server easily and safely knowing that it's already functioning in actual server's environment.

For Vote, I use a free Heroku instance for continuous deployment, and I deploy manually to my own production VPS server after that.

### Logging

If you have an app that gets steady traffic every day, how do you take your app's temperature? It seems to be running fine, your unit tests are all passing, and the app hasn't crashed since your last deployment. That's all great, but how do you know that your users aren't hitting some strange edge cases that are causing errors?

Well, you could look through your application's logs from [`forever`](https://github.com/foreverjs/forever)'s log files, but they can get very long. More importantly, how do you view only the logged errors, and logged actions leading to them, that you're most interested in for debugging, separate from other errors? What about fatal errors that you even want paged (or emailed) to you as soon as they happen?

A logger called Bunyan allows you to configure JSON logs that can include as many details as you'd like. Those JSON logs can be pretty-streamed to the console and to an external log file which an external process like [`node-log-watcher`](https://github.com/Kami/node-log-watcher) can monitor for you, and send you an email anytime something blows up. You can also stream bunyan logs programmatically, so you can make yourself an IoT wifi siren that will go off anytime Bunyan streams a POST request with a fatal error. Or not, but getting emails about errors you want to know about is very helpful.

What to log can turn into a rabbit hole, but its helpful to log information about the successful operations that affect users, and log detailed errors when things go wrong. Hopefully, you'll be able to see what operations happened leading up to the error to help narrow down what went wrong without having to inject a bunch of `console.logs`.

To set up bunyan:

{% highlight javascript %}
const bunyan = require('bunyan')
const log = bunyan.createLogger({
  name: 'vote',
  streams: [
    {
      level: 'info',
      stream: process.stdout
    },
    {
      level: 'warn',
      path: 'log/vote.log'
    }
  ]})
{% endhighlight %}

With this config, levels that are at least "info" will get streamed to `process.stout`, and levels that are at least "warn" will get logged into a file called `vote.log` in a `log` directory at the project's root. This file can be pretty-printed by running `bunyan vote.log` inside the `vote` directory.

Here's how some helpful logging can work in practice:

{% highlight javascript %}
/**
 * params: selectedOption number, pollID string, voter string
 *
 * Adds a new vote to a poll document. Returns an object
 * with the updated document and updated votes tally, or
 * error if unsuccessful.
 *
 * returns: Object
 */
const updateDocumentWithNewVote = function (selectedOption, pollID, voter) {
  const votesPath = `options.${selectedOption}.votes`
  return Poll.findOneAndUpdate(
      { _id: pollID },
      { $addToSet: { [votesPath]: { 'voter': voter } } },
      { new: true, upsert: true }
    )
    .then(updatedDoc => {
      let voteTotal = tallyVoteTotal(updatedDoc)

      log.info('pollsDb.js: updated document with new vote', { updatedDoc }, { voteTotal })

      return { updated: true, totalVotes: voteTotal, doc: updatedDoc }
    })
    .catch(err => {

      log.error('pollsDb.js: updateDocumentWithNewVote', { mongoose: true }, { err })

      return { updated: false, error: err }
    })
}
{% endhighlight %}

Note the use of `log.info` and `log.error`. Both logs will be printed to the console, but the error will also get written to the log file, which can later be searched using `bunyan`'s cli.

# Security

## [Watch this talk](https://www.youtube.com/watch?v=POmnL-PruAs&t=12s)

There are a lot of reasons not build your own authentication strategy. Good security is a constantly moving target, and nothing is immune from hackers that want to break in badly enough. Even if you manage to build a reasonably secure authentication strategy that's penetration tested and following current best practices, how often are you going to audit it for new vulnerabilities, known and unknown? Once a week? Once a month? Never? Before you know it, your users' [JWT's start getting stolen](https://auth0.com/forum/t/stealing-jwt-from-authenticated-user/352/4).

Security is very hard to get right, and its best left to professionals who deal with it full time.

## Securing Express

[Passport](http://passportjs.org/) has over 300 authentication strategies that are widely used. They're free, open source, and easier to implement that building a leaky authentication strategy yourself.

Express has a lot of [great advice](https://expressjs.com/en/advanced/best-practice-security.html) on how to secure your server. So does [Stangeloop](https://blog.risingstack.com/node-js-security-checklist/), and[RisingStack](https://blog.risingstack.com/node-hero-node-js-security-tutorial/).

[`helmet`](https://github.com/helmetjs/helmet) is mandatory.

For simple [XSS](https://www.owasp.org/index.php/Cross-site_Scripting_(XSS)) protection, [`express-sanitized`](https://www.npmjs.com/package/express-sanitized) makes it very easy.

## HTTPS

Finally, use HTTPS if you aren't already. It's very easy to set up with [certbot](https://certbot.eff.org/).

# This All Comes Back to JavaScript

You can try to learn as many frameworks, libraries and tools as you'd like, but if you don't have a good foundational understanding of JavaScript itself, you're in for a lot of pain. I've been there.

Getting a comfortable understanding of prototypal inheritance, lexical scope, closure, this, CommonJS/ES6 modules, higher-order functions, and async functions make everything else easier. You can pick up on new frameworks faster because you start to recognize the design patterns they're based on, and pick up on the conventions that many libraries follow. Think about all the libraries that using chaining functions in their APIs similar to jQuery, or open source projects that use constructor functions to build their library APIs. You begin to see the same coding patterns used over and over again, and  once you understand them, you can start working on other people's code a lot more effectively.

All it takes to get comfortable is to just put in the time. A lot of it. Talent (if that even exists) *may* give some people a head start for a time, but the people who are willing to put in the work consistently and stay curious reach new peaks every day. That can be anyone. If you want to be a "good developer", it starts with *deciding* to be a good developer, defining what that means, and doing what it takes to reach that level, and redefining a new level of mastery when you get there. That idea will change a lot over time. There are more new technologies coming out every month than you could ever hope learn. It's important to be okay with the unknowns, and hopefully find some exhilaration in the potential to discover new things every day. You can learn a lot from other developers, and you probably know a lot already that you can teach.

JavaScript is a hard language to master. It has a lot of quirks, gotchas, and [wats](https://www.destroyallsoftware.com/talks/wat), but it's powerful and expressive, and its been evolving rapidly since ES5. It's here to stay, and its everywhere. [It's even in space](https://nodejs.org/static/documents/casestudies/Node_CaseStudy_Nasa_FNL.pdf).

To get the most out of the language, it really helps to spend some time coding in JavaScript all by itself every once in awhile. No frameworks, no loDash, just ES6.

My favorite learning resource has been [You Don't Know JS](https://github.com/getify/You-Dont-Know-JS) series. Kyle Simpson goes very deep into the guts of the language in a very clear, concise way. I've jumped around the books and had to re-read a lot of the chapters over the past couple of years, but the concepts are crystallizing more and more every time I revisit them.

And again, I have to recommend Zen and the Art of Motorcycle Maintenance one more time. It will change your life.
