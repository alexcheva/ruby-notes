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

The `update` action (re-)fetches the article from the database, and attempts to update it with the submitted form data filtered by `article_params`. If no validations fail and the update is successful, the action redirects the browser to the article's page. Else, the action redisplays the form ??? with error messages ??? by rendering `app/views/articles/edit.html.erb`.

...


## Adding a second Model
The second model will handle comments on articles.

### Generating a Model
This time we'll create a `Comment` model to hold a reference to an article. Run this command in your terminal:
```
$ bin/rails generate model Comment commenter:string body:text article:references
```
This command will generate four files:
File |	Purpose
--- | ---
db/migrate/20140120201010_create_comments.rb	| Migration to create the comments table in your database (your name will include a different timestamp)
app/models/comment.rb	| The Comment model
test/models/comment_test.rb |	Testing harness for the comment model
test/fixtures/comments.yml |	Sample comments for use in testing

```
$ bin/rails db:migrate
```

### Associating Models
Active Record associations let you easily declare the relationship between two models. In the case of comments and articles, you could write out the relationships this way:

- Each comment belongs to one article.
- One article can have many comments.
In fact, this is very close to the syntax that Rails uses to declare this association. You've already seen the line of code inside the `Comment` model (`app/models/comment.rb`) that makes each comment belong to an Article:
```ruby
class Comment < ApplicationRecord
  belongs_to :article
end
```

You'll need to edit `app/models/article.rb` to add the other side of the association:
```ruby
class Article < ApplicationRecord
  has_many :comments

  validates :title, presence: true
  validates :body, presence: true, length: { minimum: 10 }
end
```
These two declarations enable a good bit of automatic behavior. For example, if you have an instance variable `@article` containing an article, you can retrieve all the comments belonging to that article as an array using `@article.comments`.  

For more information on Active Record associations, see the [Active Record Associations guide](https://guides.rubyonrails.org/association_basics.html).

### Adding a Route for Comments
As with the `articles` controller, we will need to add a route so that Rails knows where we would like to navigate to see comments. Open up the `config/routes.rb` file again, and edit it as follows:

``` ruby
Rails.application.routes.draw do
  root "articles#index"

  resources :articles do
    resources :comments
  end
end
```
This creates comments as a _nested resource_ within `articles`. This is another part of capturing the hierarchical relationship that exists between articles and comments.  

### Generating a Controller
With the model in hand, you can turn your attention to creating a matching controller. Again, we'll use the same generator we used before:
```
bin/rails generate controller Comments
```
This creates four files and one empty directory:

File/Directory	| Purpose
--- | ---
app/controllers/comments_controller.rb	| The Comments controller
app/views/comments/	| Views of the controller are stored here
test/controllers/comments_controller_test.rb |	The test for the controller
app/helpers/comments_helper.rb	| A view helper file
app/assets/stylesheets/comments.scss	| Cascading style sheet for the controller

So first, we'll wire up the Article show template (`app/views/articles/show.html.erb`) to let us make a new comment:

``` ruby
<h1><%= @article.title %></h1>
...
<h2>Add a comment:</h2>
<%= form_with model: [ @article, @article.comments.build ] do |form| %>
  <p>
    <%= form.label :commenter %><br>
    <%= form.text_field :commenter %>
  </p>
  <p>
    <%= form.label :body %><br>
    <%= form.text_area :body %>
  </p>
  <p>
    <%= form.submit %>
  </p>
<% end %>
```
This adds a form on the `Article` show page that creates a new comment by calling the `CommentsController` `create` action. The `form_with` call here uses an array, which will build a nested route, such as `/articles/1/comments`.

Let's wire up the create in `app/controllers/comments_controller.rb`:

```ruby
class CommentsController < ApplicationController
  def create
    @article = Article.find(params[:article_id])
    @comment = @article.comments.create(comment_params)
    redirect_to article_path(@article)
  end

  private
    def comment_params
      params.require(:comment).permit(:commenter, :body)
    end
end
```


