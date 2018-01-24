============
Installation
============
 

To run this application, you will have to have Node.js and MongoDB installed on your machine:

    • node.js
    • mongoDB
     

Clone the repo from GitHub:

    • git clone
 

Open the folder:

  
    • cd purdue-ironhacks
  

Install the dependencies:

  
    • npm install
  

Start MongoDB:


    • mongod or sudo mongod

=====================
Set up the environment
=====================


Before starting the app, you will have to create a file named `.env`, containing:

```env
GITHUB_CLIENT=...
GITHUB_SECRET=...
SENDGRID_KEY=...
MONGODB_URI=mongodb://localhost/purdue_ironhacks
```

You can get the GitHub keys after creating a GitHub application. Do not share these with anyone.

=========================
Starting the application
=========================

Start the app in dev mode:

```sh
npm run start:dev
```

Make yourself an admin, by passing YOUR GitHub username (the username of the
account you use to sign in for the first time):

```sh
ADMIN_USERNAME=<your-github-username> npm run start:dev
```

For example:

```sh
ADMIN_USERNAME=hackpurdue npm run start:dev
```

**Note**: The very first start takes up to 30 seconds because there is no
existing cache. After the cache is created, the next application starts will be
much faster (1-3 seconds).

=============
Deployment
=============

When deployed to Heroku, the application url is `https://<app-name>.herokuapp.com` (unless it's using a custom domain).
**Note:** When using a free dyno, it's working fine, but with some limitations:

 - it's slower
 - it's going to sleep if it's innactive for a certain period of time.
 - it has bandwidth limits, but pretty liberal

The app configuration is stored in the `bloggify.js` file.

 1. Make sure that the `heroku` remote exists (run `git remote -v` for that). If it doesn't exist, run:

    ```sh
    heroku git:remote ironhackplatform
    ```

 2. Commit all the changes and then run the following command:

    ```sh
    npm run deploy
    ```
    

============================
Work Flow and Functionality
============================

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


•GET         /
•GET         /404
•GET         /500
•GET/POST    /admin
•GET         /countdown
•GET/POST    /logout
•GET/POST    /new
•GET/POST    /register
•GET         /login
•GET         /scores
•GET         /search
•GET         /quizzes
•GET/POST    /posts/topicId-_slug/
•POST        /posts/topicId-_slug/comments
•POST        /posts/topicId-_slug/delete
•GET/POST    /posts/topicId-_slug/edit
•POST        /posts/topicId-_slug/toggle-vote
•GET/POST    /users/_user/edit
•GET         /users/_user


The `GET` method means that we fetch information from the server, while the `POST` means we post information to the server side.

The routes may have associated controllers which are located in the `app/controllers` directory.
