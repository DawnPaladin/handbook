# Ruby on Rails

## Setup

### Set up repo

```
rails new app_name
cd app_name
git init
git remote add origin <SSH address>
git push -u origin master
```

### Create a model

```
rails g model Post title:string body:string
// Examine the new migration and make any required changes
rails db:migrate
```

### Create routes

```ruby
# config/routes.rb
Rails.application.routes.draw do
  root "posts#index"
  resources :posts
end
```

Check your routes with `rails routes`.

Other things you can do in routes.rb:

```ruby
resources :posts, :only => [:index, :show]
resources :posts, :except => [:index]
```

### Create controller

```
rails g controller posts
```

```ruby
# app/controllers/posts_controller.rb
class PostsController < ApplicationController
  def index
    @posts = Post.all
  end
end
```

### Create view

```
# app/views/posts/index.html.erb
<h1>
  Posts
</h1>
<% @posts.each do |post| %>
  <h2>
    <%= post.title %>
  </h2>
  <p>
    <%= post.body %>
  </p>
<% end %>
```

### Better Errors

```
// Gemfile
group :development do
  gem "better_errors"
end
gem "binding_of_caller"
```

### Add Bootstrap

```javascript
// app/assets/javascripts/application.js

//= require bootstrap-sprockets
```

```
// Gemfile
gem 'bootstrap-sass'
```

```
/* app/assets/stylesheets/global.scss */
@import 'bootstrap-sprockets';
@import 'bootstrap';
```


## Relations

ActiveRecord objects provide an object-oriented interface to the SQL database. When you use a command like `Post.where(:published => true)`, it returns a **relation**. Relations can be comboed together, somewhat like chaining in jQuery, but the SQL query doesn't get run until you use a combo breaker that actually returns a value, like `each`, `all`, `to_a`, `inspect`, etc.

## Associations

You can inform Rails about a relationship between models like so:

```ruby
# app/models/user.rb
class User < ActiveRecord::Base
  has_many :posts
end

# app/models/post.rb
class Post < ActiveRecord:Base
  belongs_to :user
end
```
If you do that, Rails will write a lot of SQL for you, allowing you to do things like this:
```ruby
# Assume
u = User.first
p = Post.first

# All Posts written by the User
u.posts

# Build a Post with that User set as the Author
u.posts.build

# Traverse the relationship the opposite way,
# retrieving the Author of that post
p.user
```
Don't forget to specify the relationship from both directions.

Renaming a relationship:
```ruby
class Post < ApplicationRecord
  has_many :users, foreign_key: :author_id
end

class Comment < ApplicationRecord
  belongs_to :post
end

# generate a migration with rails g migration RenameUserIdToAuthorIdInComments
class RenameUserIdToAuthorIdInComments < ActiveRecord::Migration[5.0]
  def change
    rename_column :comments, :user_id, :author_id
  end
end
```

### Many-to-many relationship:
```ruby
class User < ApplicationRecord
  has_many :user_posts
  has_many :posts, through: :user_posts
end

class Post < ApplicationRecord
  has_many :user_posts
  has_many :users, through: :user_posts
end

class UserPost < ApplicationRecord
  belongs_to :post
  belongs_to :user
end
```

### Rename a many-to-many relationship:

```ruby
class User < ApplicationRecord
  has_many :user_posts
  has_many :posts, through: :user_posts
end

class Post < ApplicationRecord
  has_many :user_posts, foreign_key: :post_id
  has_many :authors, through: :user_posts, source: :user
end

class UserPost < ApplicationRecord
  belongs_to :post
  belongs_to :user
end
```
```bash
rails db:migrate
rails g migration AddAssociationColumnOrSomething
```
```ruby
class AddAssociationColumn < ActiveRecord::Migration[5.0]
  def change
    add_column :user_posts, :user_id, :integer
    add_column :user_posts, :post_id, :integer
    add_index :user_posts, [:user_id, :post_id], unique: true
  end
end
```

### More options:
```ruby
belongs_to :author, :class_name => "User" # When you need to rename the relationship. Note the use of a string instead of a symbol.
belongs_to :user, :foreign_key => :author_id # Rename the foreign key
belongs_to :category, optional: true

# if you rename the association for a X:X
# tell Rails how to traverse the join table now
class Tag < ActiveRecord::Base
  has_many  :tagged_posts,  :through => :post_taggings,
                            :source => :post
end
```
Generating the model for a many-to-many relationship:
```bash
rails g model Programmer name:string
rails g model Client name:string
rails g model Project programmer:references client:references
rake db:migrate
```
"The `:references` syntax is a shortcut for creating an index on the preceding field name, programmer and client in this instance, as well as marking them as foreign key constraints for the programmers and clients database tables." *[source](http://joshfrankel.me/blog/2016/how-to/create-a-many-to-many-activerecord-association-in-ruby-on-rails/)*

```ruby
bob = User.create
alpha = bob.posts.create
bob.posts #=> alpha

post1 = Post.create
tag1 = Tag.create
post_tag = PostTag.create(post: post1, tag: tag1)
post1.tags #=> tag1
```
## Orphans

If a user has many posts, and you a `destroy` a user, all their posts are orphans. They point to a user that no longer exists, which is bad. If you `delete` a post, all IDs pointing to it are set to nil, which also isn't great. It's best to set up links to prevent this:

```ruby
has_many :posts, :dependent => :destroy # If I die, my children go with me!
```

## Callbacks
```ruby
# Be welcoming with an after_create callback
after_create :send_welcome_email, :unless => :method_is_true
# ... and be sure to write the method to support it
private
def send_welcome_email
  # ...code to send the email
end
def method_is_true
  # code code code
end

# Get around the destroy
around_destroy :log_status
private
# ...and the method to support it:
def log_status
  puts "About to destroy Post with ID:#{self.id}"
  yield
  puts "Successfully destroyed Post with ID:#{self.id}"
end

# Using a block instead of a full method
before_save { |p| p.published_time = Time.now }
```

## Useful methods
```ruby
"User".pluralize #=> "Users"
pluralize(3,"user") #=> "3 users"
```

If the database is refusing to commit your transactions and you're not sure why, run `.errors.full_messages` on your object.

Put this in your controller to run something before each action:
```ruby
before_action :find_post, only: [:edit, :show, :update]
```
