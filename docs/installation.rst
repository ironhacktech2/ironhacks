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
