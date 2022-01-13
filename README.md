# ruby notes
Notes for Ruby
- [Documentation](https://ruby-lang.org)
### IRB (interactive ruby shell)
```ruby
irb
...
quit
```
### RI (Ruby Niformation)
```ruby
ri String
str.upcase
...
(q to qiut)
```

# Ruby on Rails Tutorial
The full tutorial is [here](https://guides.rubyonrails.org/getting_started.html).
### Ruby projects File Structure
| File/Folder	| Purpose |
| --- | :--- |
| ***app/*** | Contains the controllers, models, views, helpers, mailers, channels, jobs, and assets for your application. You'll focus on this folder for the remainder of this guide. |
| ***bin/***	| Contains the rails script that starts your app and can contain other scripts you use to set up, update, deploy, or run your application. |
| ***config/*** |	Contains configuration for your application's routes, database, and more. This is covered in more detail in [Configuring Rails Applications](https://guides.rubyonrails.org/configuring.html). |
| ***config.ru*** | Rack configuration for Rack-based servers used to start the application. For more information about Rack, see the [Rack website](https://rack.github.io/). |
| ***db/***	 | Contains your current database schema, as well as the database migrations. |
| ***Gemfile.lock***	|These files allow you to specify what gem dependencies are needed for your Rails application. These files are used by the Bundler gem. For more information about Bundler, see the [Bundler website](https://bundler.io/).
***lib/***	| Extended modules for your application.
***log/***	| Application log files.
***public/*** |	Contains static files and compiled assets. When your app is running, this directory will be exposed as-is.
***Rakefile***	| This file locates and loads tasks that can be run from the command line. The task definitions are defined throughout the components of Rails. Rather than changing Rakefile, you should add your own tasks by adding files to the lib/tasks directory of your application.
README.md |	This is a brief instruction manual for your application. You should edit this file to tell others what your application does, how to set it up, and so on.
***storage/***	| Active Storage files for Disk Service. This is covered in [Active Storage Overview](https://guides.rubyonrails.org/active_storage_overview.html).
***test/***	| Unit tests, fixtures, and other test apparatus. These are covered in [Testing Rails Applications](https://guides.rubyonrails.org/testing.html).
***tmp/***	| Temporary files (like cache and pid files).
***vendor/*** |	A place for all third-party code. In a typical Rails application this includes vendored gems.
.gitignore	| This file tells git which files (or patterns) it should ignore. See [GitHub - Ignoring files for more info about ignoring files](https://help.github.com/articles/ignoring-files).
.ruby-version	| This file contains the default Ruby version.

### Starting Ruby server
```
$ bin/rails server
```
This will start up Puma, a web server distributed with Rails by default. To see your application in action, open a browser window and navigate to http://localhost:3000. 
When you want to stop the web server, hit **Ctrl+C** in the terminal window where it's running. 
In the development environment, Rails does not generally require you to restart the server; changes you make in files will be automatically picked up by the server.

## To Say "Hello", Rails
You need to create at minimum ***a route, a controller with an action, and a view***.  
- **A route** maps a request to a controller action.  
- **A controller action** performs the necessary work to handle the request, and prepares any data for the view.  
- **A view** displays data in a desired format. 

In terms of implementation:  
- **Routes** - are _rules_ written in a Ruby [DSL (Domain-Specific Language)](https://en.wikipedia.org/wiki/Domain-specific_language).  
- **Controllers** - are Ruby _classes_, and their ***public methods*** are *actions*.  
- **Views** - are *templates*, usually written in a mixture of HTML and Ruby.  
## Declaring a route
Let's start by adding a route to our routes file, `config/routes.rb`
```ruby
Rails.application.routes.draw do
  get "/articles", to: "articles#index"

  # For details on the DSL available within this file, see https://guides.rubyonrails.org/routing.html
end
```
The route above declares that `GET /articles` requests are mapped to the `index` action of `ArticlesController`.

To create `ArticlesController` and its `index` action, we'll run the controller generator (with the `--skip-routes option` because we already have an appropriate route):
```
$ bin/rails generate controller Articles index --skip-routes
```
Rails will create several files for you. The most important of there is the controller file `app/controllers/articles_controller.rb`
``` rails
class ArticlesController < ApplicationController
  def index
  end
end
```
