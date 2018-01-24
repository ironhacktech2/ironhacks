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
