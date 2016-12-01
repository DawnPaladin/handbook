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

More options:
```ruby
has_many :post_taggings
has_many :posts, :through => :post_taggings
belongs_to :author, :class_name => "User" # When you need to rename the relationship. Note the use of a string instead of a symbol.
belongs_to :user, :foreign_key => :author_id # Rename the foreign key

# if you rename the association for a X:X
# tell Rails how to traverse the join table now
class Tag < ActiveRecord::Base
  has_many  :tagged_posts,  :through => :post_taggings,
                            :source => :post
end
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
