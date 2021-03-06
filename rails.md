**Ruby on Rails**

# Setup

## Set up repo

```
rails new app_name
cd app_name
git init
git remote add origin <SSH address>
git add -A
git commit -m "rails new"
git push -u origin master
rails db:create
```

Options for `rails new`:

- `--database=postgresql`
- `--skip-turbolinks`
- `-T` (skip testing)

## Create a model

```
$ rails g model Post title:string body:string
// Examine the new migration and make any required changes
$ rails db:migrate
```

## Create routes

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

get '/presents/:id/deliver/:receiver' => 'presents#deliver'
# in presents#deliver, look at params[:id] and params[:receiver]
```

## Create controller

```
rails g controller posts
```

```ruby
# app/controllers/posts_controller.rb
class PostsController < ApplicationController

  def index
    @posts = Post.all
  end

  def new
    @post = Post.new
  end

  def create
    @post = Post.new(strong_params)
    if @post.save
      flash[:success] = "Great! Your post has been created!"
      redirect_to @post
    else
      flash.now[:error] = "Rats! Fix your mistakes, please."
      render :new
    end
  end

  def show
    @post = Post.find(params[:id])
  end

  def edit
    @post = Post.find(params[:id])
  end

  def update
    @post = Post.find(params[:id])
    if @post.update(strong_params)
      redirect_to @post
    else
      render :edit
    end
  end

  def destroy
    Post.find(params[:id]).destroy!
    redirect_to posts_path
  end

  private
  def strong_params
    params.require(:post).permit(:title,:body,:author_id)
  end

end
```

## Create view

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

## Better Errors

```
// Gemfile
group :development do
  gem "better_errors"
  gem "binding_of_caller"
  gem "jazz_fingers"
end
```

## Remove Turbolinks

1. Remove `gem 'turbolinks'` from **Gemfile**
2. Remove `//= require turbolinks` from **app/assets/javascripts/application.js**
3. Remove both `"data-turbolinks-track" => reload` key/value pairs from **app/views/layouts/application.html.erb**

## Add Bootstrap

```javascript
// app/assets/javascripts/application.js

//= require bootstrap-sprockets
```

```
// Gemfile
gem 'bootstrap-sass'
```

Delete app/assets/stylesheets/application.css. Create an application.scss file in the same folder and add:
```
@import 'bootstrap-sprockets';
@import 'bootstrap';
```

### Viewing Flash messages in Bootstrap

```
# app/views/layouts/application.html.erb
<% flash.each do |type, msg| %>
  <%= content_tag(:div, join_messages(msg), class: "alert alert-#{type}") %>
<% end %>
```
```ruby
# app/helpers/application_helper.rb
module ApplicationHelper
  def join_messages(messages)
    messages = messages.join('; ') if messages.is_a? Array
    messages
  end
end
```

## Create Postgres database

```
sudo -i -u postgres
createdb <databasename>
```

## Start Postgres server

```
sudo /etc/init.d/postgresql start
```

## Uploading photos to AWS with Paperclip

Set up your AWS bucket and user. You can learn your region by going [here](https://console.aws.amazon.com/s3/home) and looking at the `region` parameter in the URL. For public read access, give your bucket a policy like this one, being sure to fill in your bucket name on the "Resource" line:

```
{
	"Version": "2008-10-17",
	"Statement": [
		{
			"Sid": "AllowPublicRead",
			"Effect": "Allow",
			"Principal": {
				"AWS": "*"
			},
			"Action": "s3:GetObject",
			"Resource": "arn:aws:s3:::BUCKET_NAME/*"
		}
	]
}
```

Configure your environment:

```ruby
# Gemfile
gem 'paperclip'
gem 'figaro'
gem 'aws-sdk'
```

Set up your secrets:

```
# .gitignore
/config/application.yml
```
```
# config/application.yml
development:
  AWS_HOST_NAME: us-west-2.amazonaws.com
  AWS_REGION: us-west-2
  S3_BUCKET_NAME: jamesharris-danebook
  AWS_ACCESS_KEY_ID: [redacted]
  AWS_SECRET_KEY_ID: [redacted]
```
```
# config/secrets.yml
development:
  secret_key_base: [redacted]
  s3_bucket_name: <%= ENV["S3_BUCKET_NAME"] %>
  aws_access_key_id: <%= ENV["AWS_ACCESS_KEY_ID"] %>
  aws_secret_access_key: <%= ENV["AWS_SECRET_ACCESS_KEY"] %>
  aws_region: <%= ENV["AWS_REGION"] %>
```
```ruby
# config/environments/development.rb
# This is your imagemagick directory, retrieved using `which convert`
Paperclip.options[:command_path] = "/usr/bin/convert"

config.paperclip_defaults = {
  storage: :s3,
  s3_region: "us-west-2", # your Heroku region
  s3_credentials: {
    s3_host_name: ENV['AWS_HOST_NAME'],
    bucket: ENV['S3_BUCKET_NAME'],
    access_key_id: ENV['AWS_ACCESS_KEY_ID'],
    secret_access_key: ENV['AWS_SECRET_KEY_ID'],
    s3_region: ENV['AWS_REGION']
  }
}
```
Repeat for the Production side as necessary.

Create a table in your database:
```ruby
class CreatePhotos < ActiveRecord::Migration[5.0]
  def change
    create_table :photos do |t|
      t.integer :user_id, null: false
      t.attachment :image
      t.timestamps
    end
  end
end
```

Create a model:

```ruby
# app/models/photo.rb
class Photo < ApplicationRecord
  belongs_to :user
  has_attached_file :image, styles: { medium: "300x300", thumb: "100x100"}
  validates_attachment_content_type :image, content_type: /\Aimage\/.*\Z/
end
```

Create a controller:

```ruby
# app/controllers/photos_controller.rb
class PhotosController < ApplicationController

  def index
    @user = User.find(params[:user_id])
    @profile = @user.profile
    @photos = @user.photos
  end

  def new
    @user = User.find(params[:user_id])
    @photo = current_user.photos.build
  end

  def create
    @photo = current_user.photos.build(photo_params)
    if @photo.save
      flash[:success] = "Photo uploaded!"
      redirect_to user_photos_path(current_user)
    else
      flash[:warning] = @photo.errors.full_messages
      render :new
    end
  end

  def show
    @photo = Photo.find(params[:id])
    @profile = @photo.user.profile
  end

  def photo_params
    p params
    params.require(:photo).permit(:image)
  end
end
```

Create a form in your view:

```
<%= form_for @photo do |f| %>
  <%= f.file_field :photo_data %>
  <%= f.submit %>
<% end %>
```

Show the uploaded image in your view:

```
<%= image_tag @photo.image.expiring_url %>
<!-- Or with a style: -->
<%= image_tag @photo.image.expiring_url(:thumb) %>
```

## Tests

### Unit testing the model with Factory Girl and RSpec

```
# Gemfile
group :development, :test do
  gem 'rspec-rails'
  gem 'guard-rspec', require:false
  gem 'factory_girl_rails'
end
```
```bash
$ bundle install
$ rails g rspec:install # generates /spec dir
$ rails g rspec:model User # set up a testing model for a User
$ guard init rspec # create Guardfile
```
Inside spec/rails_helper.rb, in the group of `require`s near the top, `require 'factory_girl_rails'`. In the `Rspec.configure do |config|` block, add `config.include FactoryGirl::Syntax::Methods`.


```ruby
# spec/factories.rb or spec/factories/users.rb
FactoryGirl.define do
  sequence(:name) do |i|
    "Foo#{i}"
  end

  factory :user do
    name
    email { "#{name}@bar.com" }
    password "foobar"
  end

  factory :secret do
    association :author, factory: :user
    sequence(:title) { |i| "Baz Secret #{i}"}
    body { "You won't believe what #{author}" }
  end

end
```

```ruby
# spec/models/user_spec.rb
require 'rails_helper'

RSpec.describe User, type: :model do

  let(:user) { build(:user) }
  let(:users) { create_list(:user, 3) } # create a list of 3 users

  it "is valid with default attributes" do
    expect(user).to be_valid
  end
  it "is valid with a standard password size" do
    expect(build(:user, password: "1234567890")).to be_valid
  end

end
```

[Full list of RSpec Expectations](https://github.com/rspec/rspec-expectations)

### Integration tests with Capybara

```ruby
# Gemfile
...
group :test do
  gem 'capybara'
  gem 'launchy'
end
...
```

Run `bundle install` then add the following line to your `spec/spec_helper.rb` or `spec/rails_helper.rb` file to include Capybara's functions in your tests:

```ruby
# spec/rails_helper.rb
# This file is copied to spec/ when you run 'rails generate rspec:install'
ENV["RAILS_ENV"] ||= 'test'
require 'spec_helper'
require File.expand_path("../../config/environment", __FILE__)
require 'rspec/rails'
require 'factory_girl_rails'
require 'capybara/rails'      # <<<<<<<<<
...
```

When you reload your test suite, things should work properly.

Whenever you're ready to set up a new feature spec, as we saw above, simply create a new file in spec/features which includes `rails_helper` at the top as normal. Now it's part of your normal test suite.

Note: Make sure you set up your SECRET_KEY_BASE properly in the Test Environment (under config/secrets.yml). It is necessary since Capybara basically uses your app like a normal visitor would and the app would fail to render without the SECRET_KEY_BASE properly set up.

## Emails

### Creation

```ruby
# Gemfile
group :development do
  gem 'letter_opener'
end
```
```ruby
# config/environments/development.rb
config.action_mailer.delivery_method = :letter_opener
config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }
```
Create mailer:
```
$ rails generate mailer UserMailer
```
```ruby
# app/mailers/user_mailer.rb
class UserMailer < ApplicationMailer
  default from: "welcomecommittee@crudcrud.com"

  def welcome(user)
    @user = user
    mail(to: @user.email, subject: 'Welcome to CrudCrud!')
  end
end
```
Create emails:
```
# app/views/user_mailer/welcome.html.erb
<h1>Welcome!</h1>

<p>
  Hi <%= @user.name %>, welcome to CrudCrud!
</p>

<p>
  View your account at <%= link_to "#{user_url(@user.id)}", user_url(@user.id) %>
</p>
```
```
# app/views/user_mailer/welcome.text.erb
Welcome!

Hi <%= @user.name %>, welcome to CrudCrud!

View your account at <%= link_to "#{user_url(@user.id)}", user_url(@user.id) %>
```
Send this from the Rails console with `UserMailer.welcome(User.last).deliver`. If you're on Development (and you set up LetterOpener), the email will open in your browser.

### Automatic sending
```
# Gemfile
gem 'delayed_job_active_record'
```
```bash
$ rails g delayed_job:active_record
$ rails db:migrate
```
```ruby
# config/application.rb
module NameOfApp
  class Application < Rails::Application
    #...
    config.active_job.queue_adapter = :delayed_job
  end
end
```

Add methods to model:
```ruby
# app/models/user.rb
class User < ApplicationRecord

  after_create :queue_welcome_email

  private
    def queue_welcome_email
      UserMailer.welcome(self).deliver_later
    end
end
```

Start the task that will watch for delayed jobs:
```bash
$ rake jobs:work
```
Work through all queued jobs and then exit:
```bash
$ rake jobs:workoff
```

# Deployment

## Uploading to Heroku

Remove `coffee-rails` from your Gemfile, delete app/assets/javascripts/users.coffee, and run `bundle install`.

```bash
$ heroku create # Create a new app and return its name
$ heroku git:remote -a name-of-app # Connect your Git repo to your new Heroku app
$ git push heroku master # Deploy
```

Run through "Create Postgres database" in the Setup section. Then run `heroku run rails db:migrate`.

## Securely storing authentication keys

```ruby
# Gemfile
gem 'figaro'
```
```bash
$ bundle install
$ bundle exec figaro install # create and .gitignore config/application.yml
```
```yml
# config/application.yml
SECRET_NAME: secret_value
```
```yml
# config/secrets.yml
development:
  secret_name: <%= ENV["SECRET_NAME"] %>
test:
  secret_name: <%= ENV["SECRET_NAME"] %>
production:
  secret_name: <%= ENV["SECRET_NAME"] %>
```
```ruby
# config/environments/development.rb
Rails.application.configure do
  config.some_component.configuration_hash = {
    :secret => Rails.application.secrets.secret_name
  }
```
Make sure config/secrets.yml and the files in config/environments have values for all the environments you need. Copy everything you need from development to production.

Upload values in config/environments/production.rb to Heroku:
```bash
$ figaro heroku:set -e production
```

# Links

```
<%= link_to "See All Users", users_path %> #=> <a href="/users">See All Users</a>

<%= link_to "See user", user_path(user.id) %> #= <a href="/user/1">See user</a>

<%= button_to "Edit user", edit_user_path(user.id) %> #= <a href="/user/1">See user</a>

<%= link_to "Destroy user", user_path(user.id), method: :delete %>
```

Note that in `rails routes`, `edit_user` is listed in the Prefix column, so the link is `edit_user_path`. The DELETE verb doesn't have its own prefix; you just use the user's link with the `:delete` method.

# Forms

* [More details](rails-forms.html)

* [HTML5 Form Input Validation](http://stackoverflow.com/documentation/html/277/input-control-elements/2259/input-validation#t=201612081944357410854) on StackOverflow Documentation

## Simple

```
<%= form_tag("/search", method: "get") do %>
  <%= label_tag(:query, "Search for:") %>
  <%= text_field_tag(:query) %>
  <%= submit_tag("Search") %>
<% end %>
```

## Automated

```
# app/controllers/articles_controller.rb
def new
  @article = Article.new
end

#app/views/articles/new.html.erb
<%= form_for @article do |form| %>
  <%= form.label :title %>
  <%= form.text_field :title %>
  <%= form.text_area :body, size: "60x12" %>
  <%= form.submit "Create" %>
<% end %>
```

Options go inside a hash. HTML options go inside their own sub-hash.
```
<%= form_for @post, {:html => { :class => "your_class" } } do |f| %>
```

All forms created with Rails helpers will automatically include hidden `<input>`s for encoding and Rails' required authenticity token.

## Nested

Model:

```ruby
# app/models/product.rb
class Product < ActiveRecord::Base
  has_many :category_placements
  has_many :categories, :through => :category_placements

  has_many :reviews

  accepts_nested_attributes_for :reviews,
                                :reject_if => :all_blank,
                                :allow_destroy => true;
end

# app/models/review.rb
class Review < ActiveRecord::Base
  belongs_to :product
end
```

View:

```
<%= form_for @product do |product_fields| %>

  <%= product_fields.label :name %>
  <%= product_fields.text_field :name %>
  ...

  <%= product_fields.fields_for :reviews do |review_fields| %>
    <%= review_fields.label :title %>
    <%= review_fields.text_field :title %>

    # checkbox to destroy the object
    <% if review_fields.object.persisted? %>
      <%= review_fields.label :_destroy, "Delete this review?" %>
      <%= review_fields.check_box :_destroy %>
    <% end %>

  <%= product_fields.submit "Create" %>

  <% end %>
```

Controller:

```ruby
def edit
  @product = Product.find(params[:id])
  # build an extra empty review on the association
  @product.reviews.build
end

def whitelisted_params
  params.require(:product).permit(:name, :reviews_attributes => [:title, :id, :_destroy ])
end
```

*[Source](https://www.vikingcodeschool.com/dashboard#/advanced-forms-and-active-record/a-simple-nested-form)*

## Asynchronous

Add `remote: true` to a form helper (such as `form_tag`, `form_for`, `button_to`, `link_to`...). Example:

```ruby
<%= form_for @task, :remote => true do |f| %>
```

Then set up the response in the controller method:

```ruby
respond_to do |format|
  format.js
end
```

If you only need one format, here's the one-line format: `respond_to :js`

You can optionally put a block on the end of the `format` line to do things like `redirect_to [path]` or `render :[template]`.

Rails will look for a JS template to render. If we're in the #show action, it will look in the following places:

1. show.js.erb
1. show.html.erb
1. show.*
1. [500 Internal Server Error]

Here's an example returning JSON instead of JS:

```ruby
if @tag.save
  render json: {tag: @tag}, status: 200
else
  render json: { errors: @tag.errors.full_messages }, status: :unprocessable_entity
end
```

Example of a js.erb file:

```js
$("<%= j( render partial: 'show', locals: { post: @post }) %>").hide().prependTo('#posts').slideDown();
document.getElementById('new_post').reset();
```

# Validation

```ruby
@user.persisted?
@user.new_record?
@user.valid?
```
```ruby
# app/models/post.rb
class Post < ActiveRecord::Base
  validates :title, :body, :subheading
              :presence => true, # Does it have content?
              :uniqueness => true,
              :length =>{ :maximum => 40,
                          :minimum => 10,
                          :in => 10..40, # same as above
                          :is => 16 },
              :inclusion => [ "Team Leader",
                              "Class President",
                              "Student" ],
              :format => { :with => /@/ },
              :on => :create,
              :strict => true,
              :message => "didn't work!",
              :if => :some_method_returns_true,
              :unless => Proc.new{ self.registered }
end
```
```ruby
p = Post.new
p.title = "short title"
p.valid? #=> true
p.title = "very super duper long title"
p.valid? #=> false
p.errors[:title] #=> ["is too long (maximum is 20 characters)"]
p.errors.full_messages #=> ["Title is too long (maximum is 20 characters)"]
```

# Relations

ActiveRecord objects provide an object-oriented interface to the SQL database. When you use a command like `Post.where(:published => true)`, it returns a **relation**. Relations can be comboed together, somewhat like chaining in jQuery, but the SQL query doesn't get run until you use a combo breaker that actually returns a value, like `each`, `all`, `to_a`, `inspect`, etc.

# Associations

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

## Many-to-many relationship

Generating the model:

```bash
rails g model Programmer name:string
rails g model Client name:string
rails g model Project programmer:references client:references
rake db:migrate
```

"The `:references` syntax is a shortcut for creating an index on the preceding field name, programmer and client in this instance, as well as marking them as foreign key constraints for the programmers and clients database tables." *[source](http://joshfrankel.me/blog/2016/how-to/create-a-many-to-many-activerecord-association-in-ruby-on-rails/)*

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

Making use of relationships:

```ruby
bob = User.create
alpha = bob.posts.create
bob.posts #=> alpha

post1 = Post.create
tag1 = Tag.create
post_tag = PostTag.create(post: post1, tag: tag1)
post1.tags #=> tag1
```


### Rename a many-to-many relationship

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

### Self-referencing associations

```
rails g model StarFighter ship_class:string call_sign:string
rails g model DogFights attacker_id:integer attacked_id:integer
```
```ruby
# app/models/star_fighter.rb
class StarFighter < ApplicationRecord

  has_many :outgoing_fire, foreign_key: :attacker_id, class_name: "DogFight"
  has_many :attacked, :through => :outgoing_fire

  has_many :incoming_fire, foreign_key: :attacked_id, class_name: "DogFight"
  has_many :attackers, :through => :incoming_fire

end

# app/models/dog_fight.rb
class DogFight < ApplicationRecord

  belongs_to :attacker, :foreign_key => :attacker_id, :class_name => "StarFighter"
  belongs_to :attacked, :foreign_key => :attacked_id, :class_name => "StarFighter"
  validates :attacked_id, :uniqueness => { :scope => :attacker_id }

end
```


## Renaming
```ruby
belongs_to :author, :class_name => "User" # Rename the model
belongs_to :user, :foreign_key => :author_id # Rename the foreign key
has_many :tagged_posts, :through => :post_taggings, :source => :post # Rename the relationship

belongs_to :category, optional: true
```

## Other options
```ruby
belongs_to :category, optional: true
```

## Orphans

If a user has many posts, and you a `destroy` a user, all their posts are orphans. They point to a user that no longer exists, which is bad. If you `delete` a post, all IDs pointing to it are set to nil, which also isn't great. It's best to set up links to prevent this:

```ruby
has_many :posts, :dependent => :destroy # If I die, my children go with me!
```

# Authentication

## Create users

Uncomment `bcrypt` in the Gemfile.

```ruby
# config/routes.rb
Rails.application.routes.draw do

  resource :session, only: [:new, :create, :destroy]
  get "login" => "sessions#new"
  delete "logout" => "sessions#destroy"

  resources :users, only: [:new, :create, :edit, :update]
  root "sessions#new"

end
```

```bash
$ rails g model User name:string email:string password_digest:string
```
```ruby
# app/models/user.rb
class User < ActiveRecord::Base
  has_secure_password
  validates :name, presence: true
  validates :email, presence: true
  validates :password, length: { minimum: 8 }, allow_nil: true
end
```
```
# app/views/users/new.html.erb
<%= form_for @user do |form|%>

  <%= label_tag do %>
    Username
    <%= form.text_field :name %>
  <% end %>

  <%= label_tag do %>
    Email
    <%= form.text_field :email %>
  <% end %>

  <%= label_tag do %>
    Password
    <%= form.password_field :password %>
  <% end %>

  <%= submit_tag "Sign up" %>
<% end %>
```

## Set up session-based authentication

```bash
$ rails g controller sessions
```
```ruby
# config/routes.rb
resource :session, only: [:new, :create, :destroy]
get "login" => "sessions#new"
delete "logout" => "sessions#destroy"
```
```
# app/views/sessions/new.html.erb
<%= form_tag session_path do %>

  <%= label_tag do %>
    Email
    <%= text_field_tag :email %>
  <% end %>

  <%= label_tag do %>
    Password
    <%= password_field_tag :password %>
  <% end %>

  <%= submit_tag "Log in" %>
<% end %>
```
```ruby
# app/controllers/sessions_controller.rb
class SessionsController < ApplicationController

  def create
    @user = User.find_by_email(params[:email])
    if @user && @user.authenticate(params[:password])

      sign_in(@user)
      flash[:success] = "You've successfully signed in"
      redirect_to root_url
    else
      flash.now[:error] = "We couldn't sign you in"
      render :new
    end
  end

  def destroy
    sign_out
    flash[:success] = "You've successfully signed out"
    redirect_to root_url
  end
end
```
Create helpers:
```ruby
# app/controllers/application_controller.rb
class ApplicationController < ActionController::Base
  private
    def sign_in(user)
      session[:user_id] = user.id
      @current_user = user
    end

    def sign_out
      @current_user = nil
      session.delete(:user_id)
    end

    def current_user
      @current_user ||= User.find(session[:user_id]) if session[:user_id]
    end
    helper_method :current_user

    def signed_in_user?
      !!current_user
    end
    helper_method :signed_in_user?
end
```
Add login/logout links to your navigation:
```
# app/views/layouts/application.html.erb
...
<% if signed_in_user? %>
  Welcome, <%= current_user.name %>!
  <%= link_to "Logout", logout_path, :method => :delete %>
<% else %>
  <%= link_to "Login", login_path %>
<% end %>
```

```ruby
# app/controllers/users_controller.rb
class UsersController < ApplicationController
  def new
    @user = User.new
  end

  def create
    @user = User.new(strong_params)
    if @user.save
      sign_in(@user)
      flash[:success] = "Welcome!"
      redirect_to root_path
    else
      flash.now[:error] = "Could not sign you up."
      render :new
    end
  end

  def edit
    @user = User.find(params[:id])
  end

  def update
    @user = User.find(params[:id])
    if @user.update(strong_params)
      redirect_to root_path
    else
      render :edit
    end
  end

  private
    def strong_params
      params.require(:user).permit(:name, :email, :password)
    end
end
```


## Require authorization

```ruby
# app/controllers/application_controller.rb
class ApplicationController < ActionController::Base
  before_action :require_login
  private
    def require_login
      unless signed_in_user?
        flash[:error] = "Not authorized, please sign in!"
        redirect_to login_path
      end
    end

    def require_current_user
      unless params[:id] == current_user.id.to_s
        flash[:error] = "You're not authorized to view this"
        redirect_to root_url
      end
    end
end
```

```ruby
# app/controllers/users_controller.rb
class UsersController < ApplicationController
  before_action :require_login, :except => [:new, :create]
  skip_before_action :require_login, :only => [:new, :create]
  before_action :require_current_user, :only => [:edit, :update, :destroy]
  skip_before_action :require_login, :only => [:new, :create]
end
```
```ruby
# app/controllers/sessions_controller.rb
class SessionsController < ApplicationController
  skip_before_action :require_login, :only => [:new, :create]
end
```

# Callbacks
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

# Tests

## Integration tests

Anywhere that code can be executed, try placing the method `save_and_open_page` (which requires the Launchy gem). It will open a web browser with the exact page Capybara is seeing (without JS or CSS).

```ruby
# spec/features/users_spec.rb
require 'rails_helper'

feature 'User accounts' do
  before do
    # go to the home page
    visit root_path
  end

  # `scenario` is an alias for `it`
  scenario "add a new user" do

    # go to the signup page
    click_link "New User"

    # fill in the form for a new user
    name = "Foo"
    fill_in "Name", with: name
    fill_in "Email", with: "foo@bar.com"
    fill_in "Password", with: "foobarfoobar"
    fill_in "Password confirmation", with: "foobarfoobar"

    # submit the form and verify it created a user
    expect{ click_button "Create User" }.to change(User, :count).by(1)

    # verify that we've been redirected to that user's page
    expect(page).to have_content "user show for #{name}"

    # verify the flash is working properly
    expect(page).to have_content "Created new user!"    

  end
  # ...and so on
end
```

### Navigating

```ruby
# follow links
visit('/projects')
visit(post_comments_path(post))

# use the current path
expect(current_path).to eq(post_comments_path(post))

# click things
click_link('id-of-link')
click_link('Link Text')
click_button('Save')
click_on('Link Text') # clicks on either links or buttons
click_on('Button Value')
```

### Working with Forms

```ruby
# locate a field then fill it in
fill_in('First Name', :with => 'John')
fill_in('Password', :with => 'Seekrit')
fill_in('Description', :with => 'Really Long Text...')

# select from among options
choose('A Radio Button')
check('A Checkbox')
uncheck('A Checkbox')

# perform more complex actions
attach_file('Image', '/path/to/image.jpg')
select('Option', :from => 'Select Box')
```

### Matchers

```ruby
# locate based on css-style matching
expect(page).to have_selector('table tr')
expect(page).to have_selector(:xpath, '//table/tr')

# locate based on xpath, css, or just text
expect(page).to have_xpath('//table/tr')
expect(page).to have_css('table tr.foo')
expect(page).to have_content('foo')
expect(page).to have_css('h1#header_id', text: 'Header text')

# the actual matchers behind the scenes of the above
page.has_selector?('table tr')
page.has_selector?(:xpath, '//table/tr')
page.has_xpath?('//table/tr')
page.has_css?('table tr.foo')
page.has_content?('foo')

find_field('First Name').value
find_link('Hello').visible?
find_button('Send').click

find(:xpath, "//table/tr").click
find("#overlay").find("h1").click
all('a').each { |a| a[:href] }
```

[More matchers](https://gist.github.com/them0nk/2166525)

### Scoping

```ruby
within("li#employee") do
  fill_in 'Name', :with => 'Jimmy'
end

within(:xpath, "//li[@id='employee']") do
  fill_in 'Name', :with => 'Jimmy'
end

within_fieldset('Employee') do
  fill_in 'Name', :with => 'Jimmy'
end

within_table('Employee') do
  fill_in 'Name', :with => 'Jimmy'
end
```

### Macros

```ruby
# spec/rails_helper.rb
...
# This already exists but is commented out by default
Dir[Rails.root.join("spec/support/**/*.rb")].each { |f| require f }
...
RSpec.configure do |config|
  config.include LoginMacros # or whatever you name your module
end
...
```

```ruby
# spec/support/login_macros.rb
module LoginMacros
  def sign_in(user)
    visit root_path
    click_link 'Log In'
    fill_in 'Email', with: user.email
    fill_in 'Password', with: user.password
    click_button 'Log In'
  end

  def sign_out
    visit root_path
    click_link "Logout"
  end
end
```

## Controller tests

Controller tests cover:

* authentication (before_action methods)
* redirects
* verifying flashes are set
* verifying cookies or sessions
* parameters
* all conditional logic in the controller

Controller tests do not cover:

* Instance variables
* Rendering a particular template

**Sign in**

```ruby
request.cookies['auth_token'] = user.auth_token
```

**Test that we have not redirected**
```ruby
expect(response).to have_http_status(200)
```

**Sample controller test**
```ruby
# spec/controllers/users_controller_spec.rb
require 'rails_helper'

describe UsersController do
  describe "user auth" do
    let(:user) { create :user }
    before :each do
      request.cookies["auth_token"] = user.auth_token
    end
  end

  describe '#create' do
    context "valid data" do
      it "creates a new user record" do
        expect {
          process :create, params: { user: attributes_for(:user) }
        }.to change(User, :count).by(1)
      end
      it "redirects to the user page" do
        process :create, params: { user: attributes_for(:user) }
        expect(response).to redirect_to edit_user_profile_url(User.last)
      end
    end
    context "invalid data" do
      it "does not create a new user record" do
        user_attrs = build(:user, email: "").attributes
        expect {
          process :create, params: { user: user_attrs }
        }.to change(User, :count).by(0)
      end
      # if data is invalid, the controller will re-render the form. We don't test rendering.
    end
  end
end

```

[Full tests](https://gist.github.com/DawnPaladin/977a872f74989fc521a900b47f63b08b) of a user controller and a posts controller

### Testing a JSON server

Factories:

```ruby
FactoryGirl.define do

  factory :user do
    username "username"
  end

  factory :pin do
    item_name "item_name"
    description "description"
    buy_sell true
    user
  end

end
```

Tests:

```ruby
require 'rails_helper'

RSpec.describe PinsController, type: :controller do

  describe "GET /pins.json" do

    let (:json) { JSON.parse(response.body) }

    before do
      process :index, format: :json
    end

    it 'should respond with status 200' do
      expect(response.status).to eq(200);
    end

    it 'returns a valid JSON object' do
      expect(json).to be_a Array
    end

  end

  describe "POST /pins.json" do

    let(:user) { create(:user) }
    let(:pin) { create(:pin) }

    let(:json) { JSON.parse(response.body) }

    before do
      post :create, params: { pin: attributes_for(:pin, user_id: user.id)  }, format: :json
    end

    it 'should respond with status 200' do
      expect(response.status).to eq(200);
    end

    it 'returns a valid JSON object' do
      expect(json).to be_a Hash
    end

  end

end
```


## View tests

These tests any conditional logic in your view (which shouldn't be much).

You still have access to helper methods, but not those added via `helper_method` in the controller. You will need to stub out all methods that get called.

By default, `render` only renders the specific view you're testing. You can define a method to render composite layouts:

```ruby
let(:render_with_layout){render :template => 'users/new', :layout => 'layouts/application'}
```

**Sample view test**

```ruby
require 'rails_helper'

describe "secrets/index.html.erb" do

  context "logged in user" do
  it "shows secrets' author's names" do
    user = create(:user, name: "Test user")
    secret = create(:secret, author: user)
    def view.signed_in_user?; true; end;
    def view.current_user; nil; end;
    assign(:secrets, [secret])

    render

    expect(rendered).to include(user.name)
  end
end
```

# Logging

The five levels of logging are, in ascending severity:

1. `:debug` (default in development)
2. `:info` (default in production)
3. `:warn`
4. `:error`
5. `:fatal`

To set the log level you want in an environment:
```ruby
# config/environments/production.rb
config.log_level = :warn
```
This will log anything equal to or more serious than that level.

Log information with:
```ruby
logger.debug { "Status of user: #{@user}" }
```

## Heroku logs

```bash
# just see the default # of lines
$ heroku logs

# "tail" the logs (continuously update)
$ heroku logs -t

# return a specified number of rows (1500 max)
$ heroku logs -n 500
```

# Debugging

## Stepping through code with Pry and Byebug

First, you need to install the `pry-byebug` gem. Run this command:

```
$ gem install pry-byebug
```

Add this line at the top of your .rb file:

```rb
require 'pry-byebug'
```

Then insert this line wherever you want a breakpoint:

```rb
binding.pry
```

A hello.rb example:

```rb
require 'pry-byebug'

def hello_world
  puts "Hello"
  binding.pry # break point here
  puts "World"
end
```

When you run the `hello.rb` file, the program will pause at that line. You can then step through your code with the `step` command. Type a variable's name to learn its value. Exit the debugger with `exit-program` or  `!!!`.


# Useful methods
```ruby
"User".pluralize #=> "Users"
pluralize(3,"user") #=> "3 users"
```

If the database is refusing to commit your transactions and you're not sure why, run `.errors.full_messages` on your object.

Put this in your controller to run something before each action:
```ruby
before_action :find_post, only: [:edit, :show, :update]
```
