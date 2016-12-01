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

## Useful methods
```ruby
"User".pluralize #=> "Users"
pluralize(3,"user") #=> "3 users"
```
