---
layout: post
title: Lessons Learned from My First Full Stack Application
comments: true
date: 2017-02-20T10:20:00Z
---

For the past few months, I've been working feverishly on my first full stack application challenge for Free Code Camp and it's finally [deployed](https://fcc-vote.matttrifilo.com)! You can view the source on [GitHub](https://github.com/itxchy/FCC-vote).

I took my time on this project in particular because while I was familiar with React, Express, and MongoDB at a basic level, I'd never used them together before to build a modern CRUD application. On top of that, I had no experience with Redux or the Flux patterns its based on, much less how to handle state in a large React/Redux application. This was also my first experience visualizing data with D3.js.

Needless to say, I've learned more from this project than anything else I've worked on to date, and I'm excited to share some of the most useful lessons I've learned.

## Thank You Virtual Mentors

First off, I'd like to thank Brian Holt for both of his great Complete Intro To React workshops on Front End Masters. The workflow he shared helped me to scaffold the guts of this project without endless refactors along the way as it grew. I can't recommend his workshops enough, even if you've been using React for years. The insights he shares are well worth the Front End Masters subscription. Check out all of Kyle Simpson's workshops while you're at it!

I'd also like to thank Rem Zolotykh for his great [Youtube series](https://www.youtube.com/playlist?list=PLuNEz8XtB51K-x3bwCC9uNM_cxXaiCcRY) about building a React/Redux application with jwt authentication. While you should never use a custom authentication strategy like this for real-world production apps (use Passport instead), it was valuable to learn about the authentication flow using JSON web tokens with localStorage, and managing headers. It was also great to get another perspective of how to wire up React and Redux.

Finally, I owe a great deal of gratitude to Robert M. Pirsig. While working on this project, I hit a lot of brick walls. To unwind after getting put in my place by a waterfall of error messages, I would read Zen and the Art of Motorcycle Maintenance. After picking at it for a year, I finished the second half during this project very quickly. Most of the metaphors and ideas Phaedrus was exploring correlate directly with programming. It put a lot of issues I had with code into a much broader perspective and it made me appreciate the Art of Programming much more deeply. It changed the way I percieve and approach bugs. I've gained an appreciation for them. Each hard bug illumiates a gap in knowledge with an oppurtunity to learn something new and grow as a developer--and in turn write better *quality* software and be better prepared to contribute my knowledge and experience to open source and companies building the future. A bug may seem trivial at first glance, but if you consider that the entire application relies on this trivial bug being fixed in order to run the way it ought to, it's not so trivial after all,and deserves attention and careful research. Zen and the Art of Motorcycle Maintenance should be in every CS curriculum.

## An Overview of Vote

The user stories for this Free Code Camp project can be perused [here](https://www.freecodecamp.com/challenges/build-a-voting-app).

Basically, Vote allows you to make polls as an authenticated user, vote on any poll, and share a single poll.

This project is far from perfect. You can't search or sort polls, but the time spent adding too much functionality beyond the user stories for a toy app like this is better spent on new projects at this point. I don't expect this app to go viral. Learning the process and mechanics of building something like this was the primary goal.

Let's dig in!

# Tooling

If you're going to build a good table, you better know what tools to use when, why, and how. Otherwise your food will slide into your lap.

### Linting with Standard JS (Use with caution)

I was skeptical of [Standard JS](http://standardjs.com/) at first,
but I tried it out at the recommendation of Brian Holt from his workshop, and really enjoyed the simplicity.

A lot of [very](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20&%20grammar/ch5.md#error-correction) [smart](http://www.2ality.com/2011/05/semicolon-insertion.html) [people](https://google.github.io/styleguide/javascriptguide.xml#Semicolons) assert (with good reason) that ommiting semi-colons, and relying on JavaScript's [Automatic Semicolon Insertion](https://www.ecma-international.org/ecma-262/5.1/#sec-7.9) to insert every semi-colon for you under the hood is a bad practice, and they're right! It doesn't make sense at all for a company with a team of developers to allow them to write code without semicolons because that exposes the code to yet another class of bugs that can pop up if lint rules are not followed.

That said, the [gotchas](http://standardjs.com/rules.html#semicolons) of ommitting semi-colons are well known and Standard has lint rules to stop you from making those mistakes, as long as all of your code is always linted. I think its fine for personal projects.

It's still a lot safer to use something like [Semistandard](https://github.com/Flet/semistandard) (Standard plus semicolons) for real-world code that customers will touch because including semicolons takes the possiblity of that category of bugs off the table.

#### Why Standard (or Semistandard)?

Eslint is a powerful tool, but I'd spent more time than I'd like to admit agonizing over which rules to enforce, whether to use a popular style guide from companies like AirBnB and which one, plus managing `.eslintrc` changes accross projects.

Standard JS takes all of that choice out of the equation and enforces a simple, sensible styleguide that CAN'T be changed. If you change it with custom lint rules, than you're not coding in Standard.

This idea captures the brilliance of [PEP 8](https://www.python.org/dev/peps/pep-0008/) from the Python world. If you know PEP 8 well, you'll have an easy time reading other people's Python code. Standard seeks to offer the same consistancy for JavaScript.

While I don't think it would be good for JavaScript to adopt any single style guide for every developer to adhere to at this point in its evolution, Standard offers a style guide for eslint that lets you set-it-and-forget-it, which can free up valuable brain cycles.

#### Using Standard Without React

Standard itself is very easy to [install and use](https://github.com/feross/standard#install).

The docs are straightforward, but Standard's lint errors can be a bit hard to read.

Instead of using Standard directly, you can install a package called `snazzy`, which will give you nicely formatted results with colors.

If you have a new project, here's what you can do:

Install:
```bash
$ yarn add -D snazzy
```

> If you already have standard installed globally, install `standard` as a dev dependency to work with snazzy in your project without errors:
`yarn add -D standard snazzy`. Yarn [discrourages](https://yarnpkg.com/en/docs/cli/add#toc-caveats) using global dependancies in most cases, so keep them local whenever possible for portability.

Add a lint script to `package.json`:
```
"scripts": {
  "lint": "snazzy"
}
```

> If you're using `npm`, mute the `err!` messages by adding `exit 0` to your script: `"lint": "snazzy; exit 0`

Run it:
```
$ yarn run lint
```

That's it! Standard will magically find your JavaScript (excluding `node_modules`) and return your lint errors or nothing if everything passes.

#### Using Standard With React

Using Standard with React will require an eslint config, but it's still very easy.

install:
```
$ yarn add -D eslint-config-standard eslint-config-standard-react eslint-plugin-promise eslint-plugin-react eslint-plugin-standard
```
> If you have `eslint` installed globally, add `eslint` to the above command so that your config points to the local copy in order to avoid errors.

Create a file called `.eslintrc` in your root directory and add this to it:
```
{
  "extends": ["standard", "standard-react"]
}
```

Add `eslint` and your source code directory to your lint script:
```
"scripts": {
  "lint": "eslint src"
}
```

Unlike `standard` or `snazzy`, you need to specify where eslint should look for your `.js` files. In the script above, eslint will check a directory called `src`

What if you use extensions like `.jsx` or `.es6`? `eslint` has a flag for that!:
```
"scripts": {
  "lint": "eslint --ext .js --ext .jsx src"
}
```

The CLI has many more options depending on your needs. [The docs are your friend](http://eslint.org/docs/user-guide/command-line-interface).

Finally, run it:
```
yarn run lint
```

Happy linting!

You can also delegate linting to `webpack` using `eslint-loader`, so you'll see lint errors every time a new bundle is compiled. More on that in a bit.



### Webpack 2

In a nutshell, Webpack bundles up your website's assets into a single file to reduce http requests. This lets you import things (js, jpg's, css) into your JavaScript modules, and Webpack will bundle up everything smartly so it's all in the correct order.

Very recently, Webpack 2 was [finally released](https://medium.com/webpack/webpack-2-2-the-final-release-76c3d43bf144#.plxnf4o9g) and it's awesome!

They've made the [migration process](https://webpack.js.org/guides/migrating/) almost painless. Webpack 2 will validate your config for you, so you don't need `webpack-validator` anymore. If you mess something up, Webpack will give you a helpful error message of what doesn't match their API, and how you can fix it.

The [documentation](https://webpack.js.org/configuration/) is very well thought out and organized so I encourage you to check it out if you've never used Webpack before, or haven't upgraded from version 1.

While webpack seems to perform a basic task, files in -> bundle(s) out, its configuration can get complex depending on what you need it to do. The documentation for version 2 is thankfully much better than version 1, so go through the concepts and guides on [webpack.js.org](https://webpack.js.org/) if you don't know where to start. It will get you up and running in no time.

#### Optimizing Your Bundle(s)

As your project grows, so will your bundle. It will probably get huge. The development version of Vote had a bundle that was over 3 MB before optimizing, but in production, it now has a `vendor.js` for larger dependencies, and a `bundle.js` for the app itself. Each bunlde weighs in under 200 kb minfied and gzipped. The main bundle can get broken up even furthur with [code spitting](https://webpack.js.org/guides/code-splitting/).

Before configuring the `vendor.js` bundle for Vote, I first had to figure out which dependencies were bulking up the `bundle.js` file the most.

##### Analyzing a Bundle with `webpack-bundle-analyzer`

One of the coolest plugins I've come across for Webpack is the [webpack-bundle-analyzer](https://www.npmjs.com/package/webpack-bundle-analyzer). It gives you a highly-interactive visualization of your bundle(s) to show you the elephants in the room.

It's as easy as installing:
```
$ yarn add -D webpack-bundle-analyzer
```

and including it with your plugins in your Webpack config:
```js
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin

module.exports = {

// rest of config...

  plugins: {
    new BundleAnalyzerPlugin()
  }
}

```

Now, anytime you build a new bundle, `BundleAnalyzerPlugin` will spin up a server, and open your browser to a visualiztion of your bundle(s) and dependencies scaled to size. The plugin can take a config object as an argument, but the defaults should be just fine for most cases. The config options are in the [docs](https://github.com/th0r/webpack-bundle-analyzer). To "turn it off," you can simply comment out the plugin.

For Vote, I used this tool to determine which vendor dependencies should be extracted into a `vendor.js` bundle.

##### Splitting Bundles

A lot of quick wins can be gained by splitting up your bundle. To start, putting your 3rd party libraries in a `vendor.js` bundle will not only reduce the file size of the two, but you'll also be able to take advantage of caching the 3rd party dependencies that likely won't change nearly as often as your application's code. Your users would only need to download your bulky dependencies once, and have them ready to go immediatlely on future vists. Boom! Just be sure to include a [chunkHash](https://webpack.js.org/guides/caching/) with each bundle so that browsers won't serve old cached bundles after you deploy a new build.

The ideas for splitting up bundles can get [deep](https://survivejs.com/webpack/building/splitting-bundles/) very quickly, so split them up based on how you expect your app to grow.

Some great advice on how to shink down certain dependencies, and deal with multiple copies of dependencies with different versions can be found in [this great post](https://medium.freecodecamp.com/manually-tuning-webpack-builds-284923f47f44#.799l57uja).

You can take advantage of [code splitting](https://webpack.js.org/guides/code-splitting-import/) for your application code as well, so your users only need to download the nessessary code for each "page" of your single page app, instead of the whole thing at once.

For simplicity, I decided to add just the largest libraries to Vote's `vendor.js` bundle. `d3`, `jquery`, `react`, `react-dom` and `moment`.

Those dependencies alone made the main `bundle.js` much smaller, without an overly-large `vendor.js` file.

If you have a lot more dependencies and modules in a large application, you can spit up your bundles in many different ways, but in this case for Vote, I'm not going to spend the time code splitting further since this app is not getting any larger.

#### Optimizing Bundles for Production

Making Webpack 2 builds for [production](https://webpack.js.org/guides/production-build/) can be as easy as running `webpack -p`.

That one flag will minify your JavaScript with Webpack's `UglifyJsPlugin`, set all of your loaders' minimize option to `true`, and set the Node environment variable to `true` so your code's production optimizations can take effect.

That's very handy for many situations but for Vote, I wanted to gzip my JavaScript to shrink the bundles even furthur. That required a bit of config.

A quick way to cleanly create a production config file for webpack is to use `webpack-merge`. This allows you to combine a separate config file with your main config file to limit code duplication, keep your config files clean and easy to extend. To keep things simple, I decided to simply add a `webpack.prod.js` file for production builds, and use my `webpack.config.js` file for development builds.

to install:
```
$ yarn add -D webpack-merge
```

Then, instead of exporting your config objects directly, you'll need to return the config objects as functions. Easy.

`webpack.config.js`:
```js
const webpack = require('webpack')

module.exports = function () {
  return {
    // config...
  }
}
```

The production config function will actually use `webpack-merge` to include `webpack.config.js`'s config properties as `commonConfig`.

```js
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

```

`webpackMerge` runs `commonConfig()` in its argument and recieves `webpack.config.js`'s properties.

The first three plugins are mostly identical to what `webpack` includes for you by default when passed the `-p` flag, except some extra options in `UglifyJsPlugin` here, but now I was able to add `CompressionPlugin` to gzip the `.js` files output by Webpack. We'll get into how gzipped files can be served from Express, but \*Spoiler Alert\* you can use [`express-static-gzip`](https://www.npmjs.com/package/express-static-gzip) instead of `express.static`. `express-static-gzip` is a wrapper over `express.static` that lets you serve static gzip files from a directory.

There are likely better ways to gzip files automatically from `niginx`, but my `nginx`fu is lacking on that front.

Now, you can add an npm script to build your prodution bundle:
```js
"scripts": {
  "build-prod": "webpack --config webpack.prod.js"
}
```

#### Tree-Shaking

Another amazing new feature in Webpack 2 is tree-shaking. Since Webpack 2 supports ES6 module syntax, it is now able to check for exports in your code during the bundling step that are not imported anywhere, and remove those exports. Then, when you uglify your JavaScript, all of the now-dead code gets stripped away. Since the unused modules lost their export calls, the code inside never gets touched. Thinking of this like the name "tree-shaking" implies. You have a dependency tree of code, and the branches of that tree that are never touched just fall off during the uglify step. Webpack will show you logs of the code being removed in the terminal during the uglify step.

There is a problem with this if you use Babel's `es2015` preset, but its easy to fix.

Just set `modules` to false in your `.babelrc`:
```
"presets": [
    "react",
    ["es2015", { "modules": false }]
  ],
```

Babel's `es2015` preset will convert your your ES6 modules to CommonJS modules by default, so just turn it off and your ES6 modules will be respected.

#### Linting with Webpack

Remember Standard? You can use [`eslint-loader`](https://github.com/MoOx/eslint-loader) to lint your code before Webpack bundles everything. I did this based on Brian Holt's recommendation in Complete Intro to React.

First install:
```
yarn add -D eslint-loader
```

Then, add this to your config:
```js
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
```

Note `enforce: 'pre`. That will allow your code to be linted *before* webpack's build step.

##### One Caveat

Keep in mind that `eslint-loader` will only see your code that gets bundled, so your server code will still need to be linted separately. If you're using Statnard and not Semistandard, this is doubly important to protect yourself from ASI related errors.

# The Client

Vote is a React application that leans heavily on Redux to share state across many components.

The skeleton of the application is based on Brian Holt's Complete Intro To React workshops, though I did a heavy refactor of the Redux logic as the features got more complex. I ended up using a pattern based on [Ducks](https://github.com/erikras/ducks-modular-redux), though I ended using a few differing conventions.

## React

### `React.createClass` vs ES6 classes

Since [React v0.13](https://facebook.github.io/react/blog/2015/03/10/react-v0.13.html), ES6 classes have become the defacto standard for building React components with some [helpful encouragement](https://medium.com/@dan_abramov/how-to-use-classes-and-sleep-at-night-9af8de78ccb4#.8he89ybjd) from Dan Abramov.

I recently used this style for the first time when I refactored my Free Code Camp Wikipedia search project, [Spiffy Wikipedia](https://github.com/itxchy/FCC-spiffy-wikipedia) with `create-react-app`. At first it felt annoying to have to bind each method with the constructors `this` keyword, but if you think about it, this a win from a performance standpoint since you can use a `react` replacement like `preact` which can strip away `react`'s auto-binding logic for `React.createClass` and just bind manually. Plus, it's a good idea to use stateless functional components as much as you can, until you need to use state, methods, or React lifecycle methods.

For Vote however, I used the `React.createClass` style because that's what I'd been used to, and how Brian Holt taught Complete Intro To React.

I don't really have a preference of one style over the other at this point, but I do like how I don't have to worry about forgetting a coma when using classes. Manually binding methods isn't a big deal. Looking at the broader community, classes seem to be the determined direction that most developers have committed to, so it's probably best not to fight the tide, and ensure your code is as readable as possible for the majority of React developers who are used to classes. That said, I doubt that Facebook would deprecate `React.createClass` in the foreseeable future since so many codebases rely on it.

### PropTypes

The true value of React's [PropTypes](https://facebook.github.io/react/docs/typechecking-with-proptypes.html) really shine while building larger apps.

Take this example from Vote's Create A Poll page component:
```js
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

```

There is a lot going on here, but just take a look at all of the `propTypes` checking the props coming in from Redux.

PropTypes allow you to type check your props to ensure your components receive the types they're expecting. This can catch a lot of bugs early. Using `isRequired` will throw a warning is an essential prop is ommitted.

Beyond typechecking, the `propTypes` property is great for documenting all of the props that your component is expecting.

If you're using ES6 class syntax for React, you can include your `propTypes` as a `static` property. Here's an [example](https://github.com/reactjs/redux/blob/85e2368ea9ff9b308fc873921ddf41929638f130/examples/todomvc/src/components/Header.js#L5).

Note that `static` properties are still only at [Stage-2](https://github.com/hemanth/es-next#class-property-declarations) in the [TC39 process](http://www.2ality.com/2015/11/tc39-process.html), so you'll need an extra Babel plugin called [`transform-class-properties`](https://babeljs.io/docs/plugins/transform-class-properties/) to transpile them.

Otherwise, you can take on `propTypes` to your Component `class`
separately:
```js
import React, { Component } from 'react'
const { string } = React.PropTypes

class Greeting extends Component {
  // lots of things
}

Greeting.propTypes = {
  greetingMessage: string.isRequired
}
```

Note the casing difference in `React.PropTypes` vs `Greeting.propTypes`.

### Component Hierarchy

The official React docs have a great guide about how to [think about your component structure](https://facebook.github.io/react/docs/thinking-in-react.html).

One of my favorite qualities of React is the ablitiy to compose components in your render function as JSX, effectivly treating them as if you're using custom HTML elements containing whatever you'd like and taking in any attributes you'd like. JSX is just JavaScript, so you can pass in anything to props AND regular HTML attributes that you can cram into a JavaScript expression, including arrow functions.

>Digression: One great use for arrow functions in React's synthetic DOM event attributes is when you want to pass an argument to an `onClick` event. The native `onclick` handler won't let you pass your own arguments to it since `onclick` itself receives the mouse click event as its only argument. You can get around this easily in React:
```jsx
<a
  className='btn btn-danger delete-button'
  onClick={() => this.deleteOption(index)}
  aria-label='Delete' >
  <i className='fa fa-trash-o' aria-hidden='true' />
</a>
```
This way, I was able to use an arrow function inside of `onClick`'s JavaScript expression to pass `this.deleteOption(index)` as the arrow function's implicit return value, complete with the index value passed in from the `map` function this example resides in.

How to break up your UI into components can get subjective, but it makes sense to use the [Single Responsibily Principle](https://en.wikipedia.org/wiki/Single_responsibility_principle) as mentioned in the docs. In practice, this can get very difficult as apps grow, but simple code doesn't mean easy code.

#### Spiffy Wikipedia Example

For a simple example, let's put Vote aside for a moment and look at my other recently-refactored React project, [Spiffy Wikipedia](https://github.com/itxchy/FCC-spiffy-wikipedia). This project simply allows you to search Wikipedia for a topic using its API, and the results will get displayed.

Component tree:
```
- App
  + Header
  + SearchBar
  + SearchResults
    * Result
```

`ReactDOM` renders [`App`](https://github.com/itxchy/FCC-spiffy-wikipedia/blob/master/src/components/App.js):
```js
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
```

##### Container Components

App's responsibility as a [container component](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0#.qlp6sobvr) is to handle search requests from `SearchBar`, and pass the results down to `SearchResults` after performing an AJAX call to Wikipedia's API.

As Dan Abromov puts it, container components are "concerned with *how things work.*"

Notice that this component only displays other components. The main concern of App is with handling data and passing it down to its children.

`Header`:
```js
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

```

##### Presentational Components

Header is a very simple stateless functional component. It shows the header. Notice the `Header.css` import. It may seem trivial to make a separate component for such a simple piece of UI, but now, anytime I might want to change the header's look, I know exactly where to go. Everything about the header's presentation, from markup to styling, is encapsulated right here inside this component.

`SearchBar`:
```js
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

```

##### Time to Refactor

There is a code smell here.

SearchBar is currently a presentational component with one responsibility, which is to present the search bar. It's doing that one responsibility, but with quite a bit of markup to put a form together, as well as a link to a random Wikipedia article.

Do you keep everything encapsalated here, or do you break this up with components for "SearchForm", "Button" inside "SearchForm", and "ChanceLink"? This is where things get subjective. If you're not careful, you could end up making a component for every DOM element. On the other hand, it's good for readablility and maintainability to keep components as simple as possible.

One easy compromise would be to make a `SearchForm` component to be responsible for the form itself, and leave the chance button in `SearchBar` since its only one simple link element:
```js
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
```

Now that render function is much easier to grok. I included the rest of the code here to illustrate how SearchBar's methods and state can be passed down as props.

##### Data Flow

As events happen in SearchForm, SearchForm itself doesn't alter data or handle events. SearchForm simply passes events up to SearchBar, either an input value change, or a submit event. If SearchBar gets an input change event from `this.handleSearchInputChange`, called from SearchForm, SearchBar updates its state, and passes that new state down to SearchForm's `searchText` prop as the new data. SearchForm can than update the form with the new data it receives. When SearchForm's form is submitted, it can call `this.handleSubmit`, which it received as a `handleSubmit` prop from SearchBar, and SearchBar will handle it by passing the form data up to App using App's `handleSearchSubmit` method, which App passed down to SearchBar as a prop called `onSearchSubmit`. SearchBar doesn't care about how to handle search submits.

The important takeaway is that data always moves in one direction. This is the essence of one-way data-binding. If anything breaks, its easy to trace where  something went wrong because data is moving only in a single direction when an event happens. Typing something into the form doesn't update the DOM form directly like you'd expect nativly. Instead, your keystroke is sending an event, which eventually culminates with React updating the form's text from its own state. This happens so fast, it feels native. This is called a [controlled component](https://facebook.github.io/react/docs/forms.html). React is handling the form state, instead of allowing the DOM to handle form state. Debugging errors in this sort of flow is a lot easier than in other paradigms where data can be passed in both directions. Two-way data binding makes it hard to determine the source of bugs in a lot of cases if you don't know where the data is coming from when a bug occurs. Thankfully, Angular has moved passed that, and a number of other frameworks have embraced one-way data-binding.

[React Dev Tools](https://facebook.github.io/react/blog/2015/09/02/new-react-developer-tools.html) even allow you to watch state updates in real time!

##### Escalating to Redux

Now, what if you wanted to add a dropdown of look-ahead search results SearchForm?

It would make sense to include that UI as a separate component, but what if it needs to call `loadWikiData` from [`App`](https://github.com/itxchy/FCC-spiffy-wikipedia/blob/master/src/components/App.js) to load results every 400ms?

Let's reference our updated component tree:
```
- App (WikiContainer)
    + SearchBar
      * SearchForm
        - LookAheadDropdown
          + LookAheadResult
    + SearchResults
      * Result
```

We don't need to flesh out that idea in code to realize we'd have a big problem. To do this, we'd need to pass `this.loadWikiData` down as a prop through SearchBar, into SearchForm, and possibly even LookAheadDropdown depending on if we end up refactoring SearchForm to include handleSearchInputChange, and handleSubmit instead of [SearchBar](https://github.com/itxchy/FCC-spiffy-wikipedia/blob/master/src/components/SearchBar/SearchBar.js) to furthur clarify each components' single responsibility as this app grows. But that's not all! We'd need to pass `this.state.data` from App down the tree to at least SearchForm as well.

This is called the data tunneling problem. As the app grows, so do the number of components that data and events need to pass through. This is when Redux makes its entrance.

## Redux

Redux is a powerful Flux pattern variant that allows you to abstract away all of your React application's state, and pass bits of state to components as props based on what they need. You can pass action creaters as props too, which are basically state-altering functions, among any components that need them.

For big apps, this allows you to decouple your components so they don't need to worry about the flow of state, state-altering methods, or events being passed through the component tree. It all flows in from Redux, a la carte style.

### When Do You Need Redux?

