============
Installation
============

Install the package with pip::

    $ node.js
    $ mongoDB
    

Install the package with pip::

    $ pip install read-the-docs-template
    $ pip install read-the-docs-template


Install the package with pip::

    $ pip install read-the-docs-template
    

Install the package with pip::

    $ pip install read-the-docs-template
    

To run this application, you will have to have Node.js and MongoDB installed on your machine.

    $ node.js
    $ mongoDB

Then you can proceed to the next steps:

 1. Clone the repo either from GitHub or Heroku:

  
    $ git clone https://github.com/hackpurdue/courseswebpage.git
  

  or Heroku (this is not recommended, but it will still work as long you have access to the project):

  
    $ git clone https://git.heroku.com/ironhackplatform.git courseswebpage
  

  **Note**: This is not recommended because the code pushed to Heroku is a little
  bit different than the version we push to GitHub. When deploying the app on
  Heroku, we bundle the files **before** pushing them, therefore the Heroku
  version will store the production bundles, while the GitHub version doesn't
  store any bundles at all.

  There are two reasons why we bundle the files before deployment:

   1. Heroku is going to complain if we do it on their servers because it's a
      very resource-consuming process (it's using RAM memory, eventually
      exceeding it).

      Doing it on our machines is much better because we have enough RAM.

   2. The boot time of the app will be much faster (because no bundles have to
      be generated on Heroku).

2. Open the folder:

  
   $ cd courseswebpage
  

3. Install the dependencies:

  
  $ npm install
  

    Start MongoDB:


  $ mongod
    # or
    sudo mongod

