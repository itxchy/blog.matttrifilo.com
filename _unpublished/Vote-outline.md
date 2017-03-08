# Outline

Lessons Learned From my First Full Stack Application

- Introduction
    + Who is this post for?
    + Fill in the gaps in knowledge
    + Motivation: Completing user stories and beyond
    + Thank you Brian Holt, Dan Abramov for insights in almost every stackoverflow question and Redux issue I've encountered
- Overview of Vote
    + What is Vote?
        * broad description
        * user stories
    + Standard JS
        * No esLint config considerations to think about, beyond standard-react
        * enforces propTypes!
        * Thanks Brian Holt
    + Webpack
        * webpack-validator
        * pre-loader linting
        * extracting css out of the bundle
    + Client: React, Redux, SASS, Bootstrap, Webpack
        * React
            - createClass vs ES6 classes
            - propTypes are mandatory. Flow is a great option
              + https://babeljs.io/blog/2015/06/07/react-on-es6-plus
            - When to break apart components
            - Smart vs dumb components
            - When is Redux needed?
        * Redux
            - Dev Tools
            - Module pattern
            - Thunks
            - Lib functions
            - Testing
                + What to test, and what not to test (some thunks, side effects)
            - Error logging
        * SASS
            - import the main SASS file, and webpack will take care of the rest
            - global JS libraries (jQuery, Bootstrap.js) imported in BrowserEntry.jsx
                + optimization considerations
    + Server: SSR, API, Authentication
        * Babel Register
            - exclude node_modules to prevent strange errors
            - production caveats
        * Server Side Rendering
            - Thanks Brian Holt!
            - Advantages
                + Shared code example
                    * validating form values on the client and server using same code
                + much faster initial page loads for slow connections
                + better SEO
            - Creating history to fix basename issues
            - match() function
                + importing routes
                + handling callback arguments (error, redirectLocation, renderProps)
                + rendering React to a string
                    * createElement refresher
                    * loDash templates
        * Custom Authenticate middleware
            - Thanks bro
        * API
            - Express routes
            - CRUD: get, post, put, delete
            - API Design
    + Mongo: Mongoose, Dev DB vs Production
        * Dev database vs deployed database
        * Models and Schema design
        * Mongoose
            - use promises to your advantage
        * async await
    + Production
        * Deployment options
        * CirlceCI
            - advantages
        * Deploying to Heroku with CircleCI
            - staging vs production
        * server logging
            - Bunyon!
-  Testing
    +  TDD: Perils of code first, test later
    +  Jest wins: Snapshots, ease of use, no extra browser window
-  Security
    +  Auth implementation dangers
    +  Securing Mongo


## Express API Routes

stateless,
explicit HTTP verbs, GET POST PUT DELETE
performs CREATE READ UPDATE DESTROY operations (CRUD)
expose a directory like structure for routes leading to a resource
transfer JSON (or XML) and consumes it

like SOAP

anticipate your model, design your api

## Jest

### Snapshot Testing
snapshots, rendered component should match snapshot

the component gets rendered completely each time, and compared with the snapshot. It basically tests that all of the markup renders the way you're expecting.

You can pass in dummy data to props, like a faked JSON response from an API and you can quicky test that the component renders the data properly.

Not the most robust tests, but they're fast.

### Jest wins

Only files that change get retested in watch mode. very fast.

### Enzyme

enzyme from Air BnB, great for testing React components with tools like shallow and mount

You can make your own events with `simulate`

DOM api use requires render



### advice

