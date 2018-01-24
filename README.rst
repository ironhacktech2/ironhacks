Template
========

$project will solve your problem of where to start with documentation,
by providing a basic explanation of how to do it easily.

Look how easy it is to use:

    import project
    # Get your stuff done
    project.do_stuff()

Features
--------

- Be awesome
- Make things faster

Installation
------------

Install $project by running:

    install project

Contribute
----------

- Issue Tracker: github.com/$project/$project/issues
- Source Code: github.com/$project/$project

Support
-------

If you are having issues, please let us know.
We have a mailing list located at: project@google-groups.com

License
-------

The project is licensed under the BSD license.



### Workflow and functionality

Being a Bloggify application, the application configuration is kept in a file: `bloggify.js`. This contains (see the inline comments):

```js
"use strict";

const conf = require("bloggify-config");

// Set the right MongoDB URI (depending on the environment).
const DB_URI = process.env.MONGODB_URI
if (!DB_URI) {
    console.error(">>>> Please provide the MongoDB URI. Set the MONGODB_URI environment variable.");
}

module.exports = conf({
    // Application metadata
    title: "IronHacks"
  , description: "Hack for inovation and join the open data movement."

    // The production domain
  , domain: "http://www.ironhacks.com"

    // Core plugins (which are initialized before the others)
  , corePlugins: [
        "bloggify-mongoose"
    ]

    // Application plugins
  , plugins: [
        "bloggify-sendgrid"
      , "bloggify-custom-assets"
      , "bloggify-github-login"
    ]

    // The application router
  , router: "bloggify-flexible-router"

    // We do not have a blog page, so we do not need a Bloggify viewer at all
  , viewer: null

    // Plugins configuration
  , config: {

        // Custom application assets
        "bloggify-custom-assets": {
            styles: [
                "app/assets/stylesheets/index.css"
            ]
          , server: [
                "app/server/index.js"
            ]
        }

        // The application router
      , "bloggify-flexible-router": {
            controllers_dir: "app/controllers"
          , routes_dir: "app/routes"
          , error_pages: {
                404: "404.ajs"
              , 500: "500.ajs"
              , bad_csrf: "422.ajs"
            }
        }

        // Login with GitHub
      , "bloggify-github-login": {
            githubClient: process.env.GITHUB_CLIENT
          , githubSecret: process.env.GITHUB_SECRET
        }

        // Connect to the MongoDB database
      , "bloggify-mongoose": {
            db: DB_URI
          , models_dir: "app/models"
        }

        // Send emails
      , "bloggify-sendgrid": {
            key: process.env.SENDGRID_KEY
        }
    }
}, {
    cms_methods: false
  , server: {
        session: {
            storeOptions: {
                url: DB_URI
            }
        }
    }
});
```

The way how this Bloggify application is structured is explained below.

The `app` directory contains the application files

The application routes (urls) are:


*GET         /
*GET         /404
*GET         /500
*GET/POST    /admin
*GET         /countdown
*GET/POST    /logout
*GET/POST    /new
*GET/POST    /register
*GET         /login
*GET         /scores
*GET         /search
*GET         /quizzes
*GET/POST    /posts/topicId-_slug/
*POST        /posts/topicId-_slug/comments
*POST        /posts/topicId-_slug/delete
*GET/POST    /posts/topicId-_slug/edit
*POST        /posts/topicId-_slug/toggle-vote
*GET/POST    /users/_user/edit
*GET         /users/_user


The `GET` method means that we fetch information from the server, while the `POST` means we post information to the server side.

The routes may have associated controllers which are located in the `app/controllers` directory.
