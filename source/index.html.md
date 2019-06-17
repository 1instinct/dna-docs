---
title: DNA Docs

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - ruby
  - python
  - javascript

toc_footers:
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

search: true
---

# Introduction

Welcome to the technical documentation for the Material Instinct LLC - DNA Boilerplate.<%= config[:host] %>

# Local installation (without Docker)

The platform has 4 parts which need to be installed in this order:  
- DNA Admin  
- DNA Database  
- DNA API  
- DNA Frontend  
You will need access to each individual repository, or you will need to be added to an internal team with the appropriate access privileges.

Requirements: ruby 2.6.2, rails 5.2.2, Postgres

## DNA Admin (Github repo: `dna-admin`)

Admin app for Spree.

Local installation:

1. Make sure you have `ImageMagick` installed; on Mac OSX you can do:  
`brew install imagemagick`  
Brew will install ImageMagick in a directory similar to the following:   
`/usr/local/Cellar/imagemagick/7.0.8-49/bin/magick`  
You can add this dir to your PATH, for instance append the following to your `~/.bashrc` file:  
`export PATH=$PATH:/usr/local/Cellar/imagemagick/7.0.8-49/bin`  
followed by the command  
`source ~/.bashrc`  
1. Clone this repo
1. Copy `.env.example` to `.env.development`
1. Edit `.env.development`:  
Change `DATABASE_PORT` from `2345` to `5432`  
Change `COMPANY_LOGO` from `path/to/file.png` to `dwell_logo.png`  
1. Create the PostgreSQL user that we need:  
`createuser --pwprompt dna_admin`  
At the prompt, enter the DB password from the `.env.development` file.
1. Create the PostgreSQL database and assign permissions:  
`# Login into PostgreSQL with the command line client -`  
`# replace 'user' with your admin/root user name:`  
`psql -U user postgres`  
`# Then, within the psql shell, issue the commands:`  
`CREATE DATABASE dna_admin_dev;`  
`GRANT ALL PRIVILEGES ON DATABASE dna_admin_dev to dna_admin;`  
`ALTER DATABASE dna_admin_dev owner to dna_admin;`  
**Make sure the database creds (e.g. "dna_admin" in the commands above) match those in `.env.development` !**
1. Install dependencies:  
`bundle install`
1. Issue the following rails/rake commands to install Spree (including database data and image assets):  
`rails g spree:install --user_class=Spree::User`  
`rails g spree:auth:install`  
`rails g spree_gateway:install`  
**Notes:**  
While running these `rails g spree` commands, you will receive some warnings:  
`conflict  config/initializers/spree.rb`  
`Overwrite ...config/initializers/spree.rb? (enter "h" for help) [Ynaqdhm]`  
and  
`conflict  config/initializers/devise.rb`  
`Overwrite ...config/initializers/devise.rb? (enter "h" for help) [Ynaqdhm]`  
Answer 'Y' to these prompts.  
The command `rails g spree:install --user_class=Spree::User` will also ask for the admin user/password:  
`Create the admin user (press enter for defaults).`  
`Email [spree@example.com]:`  
`Password [spree123]:`  
You can accept these defaults (or override them, and remember the values you entered).  
1. The above `rails g spree` commands have made a few modifications which we don't want - to revert these, do:  
`git checkout -- config/application.rb config/initializers/spree.rb config/routes.rb \`  
`  config/initializers/devise.rb vendor/assets/javascripts/spree/frontend/all.js`

Do not attempt to start the app yet (`rails s`) - we need to complete the database setup first.

## DNA Database (Github repo: `dna-database`)

This is used to manage (migrations, schema) the single database for all of the other apps.

Local installation:

1. Clone this repo
1. Copy `.env.example` to `.env.development`
1. Edit `.env.development`: change `DATABASE_PORT` from `2345` to `5432`
1. Install dependencies:  
`bundle install`
1. Issue the following rake command:  
`rake db:migrate`  

## DNA API (Github repo: `dna-api`)

Local installation:

1. Clone this repo
1. Copy `.env.example` to `.env.development`
1. Edit `.env.development`:  
Change `DATABASE_PORT` from `2345` to `5432`  
Give PUSHER_APP some value, e.g. `PUSHER_APP_ID=foo` (otherwise the app won't start)  
1. Install dependencies:  
`bundle install`

## Testing your installation

Move into the 'dna-admin' directory, execute `rails s` and enter http://localhost:3000 in your browser.  
You should see a product listing with images.

Enter http://localhost:3000/admin in your browser and log in with spree@example.com/spree123 or with whatever you chose for the admin user. You can manage the system here.

Next move into the 'dna-api' directory and execute:  

`PORT=3001 rails s`  

and enter http://localhost:3001 in your browser - this will show the 'index' page.  

You can enter `rails routes` on the command line to see which routes the API supports. On of them is `GET v1/products`, so we can enter in the browser:

`http://localhost:3001/v1/products`

to get a complete listing of products from the Spree product catalog. The reason why this work is simply a "resource route" definition for Products in `config/routes.rb`, which then generates a set of CRUD routes on the product table.


