# Ruby on Rails

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

Many-to-many relationship:
```ruby
class User < ApplicationRecord
  has_many :user_posts
  has_many :posts, through: :user_post
end

class Post < ApplicationRecord
  has_many :user_posts
  has_many :users, through: :user_post
end

class UserPost < ApplicationRecord
  belongs_to :post
  belongs_to :user
end
```

More options:
```ruby
belongs_to :author, :class_name => "User" # When you need to rename the relationship. Note the use of a string instead of a symbol.
belongs_to :user, :foreign_key => :author_id # Rename the foreign key

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

### Orphans

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
