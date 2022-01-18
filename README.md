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
To learn more about routing, see [Rails Routing from the Outside In](https://guides.rubyonrails.org/routing.html).

## Genarating a model
**A model** is a *Ruby class* that is used to represent data. Additionally, models can interact with the application's database through a feature of Rails called ***Active Record***.

To define a model, we will use the model generator:

```
$ bin/rails generate model Article title:string body:text
```

## Using a Model to interact with the database
```
$ bin/rails console
```
At the irb prompt, we can initialize a new `Article` object:

```ruby
irb> article = Article.new(title: "Hello Rails", body: "I am on Rails!")
...
irb> article.save
```
To fetch this article from the database, we can call **.find** on the model and pass the `id` as an argument or use **.all**
``` ruby
irb> Article.find(1)
...
irb> Article.all
```
**.all** This method returns an [ActiveRecord::Relation](https://api.rubyonrails.org/v7.0.1/classes/ActiveRecord/Relation.html) object, which you can think of as a super-powered array.

To learn more about models, see [Active Record Basics](https://guides.rubyonrails.org/active_record_basics.html) and [Active Record Query Interface](https://guides.rubyonrails.org/active_record_querying.html).

## Showing the single article
Open `config/routes.rb`, and insert the last route shown here:
```ruby
Rails.application.routes.draw do
  root "articles#index"

  get "/articles", to: "articles#index"
  get "/articles/:id", to: "articles#show"
end
```
Let's add that `show` *action* now, below the index action in `app/controllers/articles_controller.rb`:
```ruby
class ArticlesController < ApplicationController
...
  def show
    @article = Article.find(params[:id])
  end
end

```
Let's create `app/views/articles/show.html.erb`, with the following contents:
```<h1><%= @article.title %></h1>
<p><%= @article.body %></p>
```
We'll link each article's title in `app/views/articles/index.html.erb` to its page:
```
<h1>Articles</h1>

<ul>
  <% @articles.each do |article| %>
    <li>
      <a href="/articles/<%= article.id %>">
        <%= article.title %>
      </a>
    </li>
  <% end %>
</ul>

```
## Resourceful Routing
Rails provides a routes method named [resources](https://api.rubyonrails.org/v7.0.1/classes/ActionDispatch/Routing/Mapper/Resources.html#method-i-resources) that maps all of the conventional routes for a collection of resources, such as articles. Let's replace the two get routes in `config/routes.rb` with resources:
```ruby
Rails.application.routes.draw do
  root "articles#index"

  resources :articles
end

```
We can inspect what routes are mapped by running the `bin/rails routes` command:
```
$ bin/rails routes
...
Prefix Verb   URI Pattern                  Controller#Action
        root GET    /                            articles#index
    articles GET    /articles(.:format)          articles#index
 new_article GET    /articles/new(.:format)      articles#new
     article GET    /articles/:id(.:format)      articles#show
             POST   /articles(.:format)          articles#create
edit_article GET    /articles/:id/edit(.:format) articles#edit
             PATCH  /articles/:id(.:format)      articles#update
             DELETE /articles/:id(.:format)      articles#destroy
```
The `resources` method also sets up ___URL and path helper methods___ that we can use to keep our code from depending on a specific route configuration. The values in the "Prefix" column above plus a suffix of `_url` or `_path` form the names of these helpers. For example, the `article_path` helper returns `"/articles/#{article.id}"` when given an article. We can use it to tidy up our links in `app/views/articles/index.html.erb`:
``` ruby
<h1>Articles</h1>

<ul>
  <% @articles.each do |article| %>
    <li>
      <a href="<%= article_path(article) %>">
        <%= article.title %>
      </a>
    </li>
  <% end %>
</ul>

```
To learn more about __routing__, see [Rails Routing from the Outside In](https://guides.rubyonrails.org/routing.html).  

## Creating a New Article

In a Rails application, these steps are conventionally handled by a controller's `new` and `create` actions. Let's add a typical implementation of these actions to `app/controllers/articles_controller.rb`, below the show action:
```ruby
class ArticlesController < ApplicationController
  def index
    @articles = Article.all
  end

  def show
    @article = Article.find(params[:id])
  end

  def new
    @article = Article.new
  end

  def create
    @article = Article.new(title: "...", body: "...")

    if @article.save
      redirect_to @article
    else
      render :new, status: :unprocessable_entity
    end
  end
end
```
To finish up, let's link to that page from the bottom of `app/views/articles/index.html.erb`:
```ruby
<h1>Articles</h1>

<ul>
  <% @articles.each do |article| %>
    <li>
      <%= link_to article.title, article %>
    </li>
  <% end %>
</ul>

<%= link_to "New Article", new_article_path %>
```
## Updating an Article
These steps are conventionally handled by a controller's `edit` and `update` actions. Let's add a typical implementation of these actions to `app/controllers/articles_controller.rb`, below the `create` action:
```ruby
class ArticlesController < ApplicationController
 ...

  def edit
    @article = Article.find(params[:id])
  end

  def update
    @article = Article.find(params[:id])

    if @article.update(article_params)
      redirect_to @article
    else
      render :edit, status: :unprocessable_entity
    end
  end

  private
    def article_params
      params.require(:article).permit(:title, :body)
    end
end

```
The `edit` action fetches the article from the database, and stores it in `@article` so that it can be used when building the form. By default, the edit action will render `app/views/articles/edit.html.erb`.

The `update` action (re-)fetches the article from the database, and attempts to update it with the submitted form data filtered by `article_params`. If no validations fail and the update is successful, the action redirects the browser to the article's page. Else, the action redisplays the form — with error messages — by rendering `app/views/articles/edit.html.erb`.


