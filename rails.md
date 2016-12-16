**Ruby on Rails**

# Setup

## Set up repo

```
rails new app_name # optional: --database=postgresql
cd app_name
git init
git remote add origin <SSH address>
git push -u origin master
```

## Create a model

```
rails g model Post title:string body:string
// Examine the new migration and make any required changes
rails db:migrate
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
      # Woohoo
      redirect_to @post
    else
      # boohoo
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

## The Flash ![The Flash](img/Flash.png)

```ruby
# app/controllers/posts_controller.rb
def create
  @post = Post.new(params[:post])
  if @post.save
    flash[:success] = "Great! Your post has been created!"
    redirect_to @post # go to show page for @post
  else
    flash.now[:error] = "Rats! Fix your mistakes, please."
    render :new
  end
end
```

Viewing Flash messages in Bootstrap:
```
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

## Better Errors

```
// Gemfile
group :development do
  gem "better_errors"
  gem "binding_of_caller"
  gem "jazz_fingers"
end
```

## Add Bootstrap

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

## Create Postgres database

Add "pg" to your Gemfile.

In config/database.yml, under "default", change "adapter" to "postgresql". Under "development", change "database" to "devise_development", under "test" change it to "devise_test", and under "production" change it to "devise_production".

Then run `rails db:create`.

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
config.action_mailer.default_url_options = { host: 'localhost:3000' }
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

# Links

```
<%= link_to "See All Users", users_path %> #=> <a href="/users">See All Users</a>

<%= link_to "See user", user_path(user.id) %> #= <a href="/user/1">See user</a>

<%= button_to "Edit user", edit_user_path(user.id) %> #= <a href="/user/1">See user</a>

<%= link_to "Destroy user", user_path(user.id), method: :delete %>
```

Note that in `rails routes`, `edit_user` is listed in the Prefix column, so the link is `edit_user_path`. The DELETE verb doesn't have its own prefix; you just use the user's link with the `:delete` method.

# Forms

[HTML5 Form Input Validation](http://stackoverflow.com/documentation/html/277/input-control-elements/2259/input-validation#t=201612081944357410854) on StackOverflow Documentation

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

# Validation

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

# Orphans

If a user has many posts, and you a `destroy` a user, all their posts are orphans. They point to a user that no longer exists, which is bad. If you `delete` a post, all IDs pointing to it are set to nil, which also isn't great. It's best to set up links to prevent this:

```ruby
has_many :posts, :dependent => :destroy # If I die, my children go with me!
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

Full tests of a [user controller](https://github.com/DawnPaladin/assignment_rspec_secrets/blob/Jessica/spec/controllers/users_controller_spec.rb) and a [posts controller](https://github.com/DawnPaladin/assignment_rspec_secrets/blob/Jessica/spec/controllers/secrets_controller_spec.rb)

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
