**Rails forms**

# Building forms

Including your own form authenticity token (if you're not using one of Rails' forms)
```html
<input  type="hidden"
        name="authenticity_token"
        value="<%= form_authenticity_token %>">
```
To nest data in params, use brackets [] in the `name` attribute
```html
<input type="text" name="post[title]">
<input type="text" name="post[body]">
```
...which gives us params like this:
```ruby
Parameters: { "utf8"=>"✓",
              "authenticity_token"=>"wd4R0OmkSallVj+rey34+8TCKtExrs6WTEuexx12dh8=",
              "post"=>{ "title"=>"Cool Title",
                        "body"=>"Cooler Body" },
              "commit"=>"Submit Post"}
```
`form_for` is the fastest way to build forms.
```
<%= form_for @post, :html => {:class => "nifty_form"} do |f| %>
  <%= f.text_field :title %>
  <%= f.text_area :body, size: "60x12" %>
  <%= f.submit "Create" %>
<% end %>
```
```
<form accept-charset="UTF-8" action="/articles/create" method="post">
  <input id="article_title" name="article[title]" type="text" />
  <textarea id="article_body" name="article[body]" cols="60" rows="12"></textarea>
  <input name="commit" type="submit" value="Create" />
</form>
```

# Validation

```ruby
@user.persisted?
@user.new_record?
@user.valid?

# Sample validation with all the options
validates :title, :body, :subheading
            :presence => true,
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

# Skipping validation
@user.save(:validate => false)

# Accessing errors
@user.errors                # ActiveModel::Errors object
@user.errors[:name]         # Array (just one string)
@user.errors.full_messages  # Array (potentially many strings)
```

# Check boxes

```ruby
# basic check box tag
# signature: check_box_tag(name, value = "1", checked = false, options = {})
check_box_tag(:tag, 1234)

# check box (form_for)
# signature: check_box(object_name, method, options = {}, checked_value = "1", unchecked_value = "0")
check_box(:tag, :id)
f.check_box(:id)
f.check_box(:id, true, false) # Specifying "checked" & "unchecked" values
```

## Collection check boxes
Signature:
```ruby
collection_check_boxes( object,
                        method,
                        collection,
                        value_method,
                        text_method,
                        options = {},
                        html_options = {},
                        &block )
```
* `object` is a strange one. It takes a symbol containing the name of the instance variable containing an instance of the class of your parent object. So if you render your form and the parent object is contained in an instance variable named `@post`, `object` would be  `:post`. If you had named your instance variable `@posterino`, this would need to be `:posterino`.
* `method` is the method to call on your parent instance in order to retrieve the IDs of the objects which are currently associated with it. For example, `:tag_ids` in this case. This is used when rendering the form to figure out which checkboxes should be checked and it is also used to properly name each checkbox.
* `collection` is the collection of possible options for your checkboxes, for example `Tag.all`
* `value_method` is the method to call on a current child object to populate the `value` field of the checkbox. For example, `:id`.
* `name_method` is the method to call on the current child object to populate the `name` field of the checkbox. For example, `:name`.

Example:

```ruby
# collection-based check boxes (form_for)
collection_check_boxes(
  :post,     # Our instance variable is @post
  :tag_ids,  # Run @post.tag_ids to get current tag ids
  Tag.all,   # The collection of possible tags
  :id,       # Run `id` on each tag to see if it's in the association
  :name      # Run `name` on each tag to get its text
) # also generates a hidden blank value in case all the boxes are unchecked

# On a FormBuilder object (from `form_for`)
f.collection_check_boxes(:tag_ids, Tag.all, :id, :name)
# same as previous, except you're calling it on :post instead of passing :post in as a param
```

# Radio buttons

```ruby
# basic radio button tag helper (`form_tag`)
# signature: radio_button_tag(name, value, checked = false, options = {})
radio_button_tag(:age, "child")
radio_button_tag(:age, "adult")

# basic radio button helper (`form_for`)
# signature: radio_button(object_name, method, tag_value, options = {})
radio_button("user", "receive_newsletter", "yes")
radio_button("user", "receive_newsletter", "no")
f.radio_button("receive_newsletter", "yes")
f.radio_button("receive_newsletter", "no")
```

## Collection radio buttons
Signature:

```ruby
collection_radio_buttons(  object,
                                method,
                                collection,
                                value_method,
                                text_method,
                                options = {}, # important!
                                html_options = {},
                                &block)
```

The only difference with the `collection_check_boxes` is that, because radio buttons represent one-to-many collections where you are setting the "one" side, the `method` parameter is simply the foreign key (e.g.  `:author_id`) instead of the collection of IDs (e.g. `:tag_ids`).

Examples:

```ruby
# First show how it would be done the `form_tag` way:
<%= form_tag "/posts/1" do %>
  <%= collection_radio_buttons(
      :post,          # assume `@post` contains a post instance
      :author_id,     # the foreign key for Authors
      User.limit(5),  # To only show 5 user options for example
      :id,
      :name )
  %>
  ...
<% end %>

# Now show the `form_for` way:
<%= form_for @post do |post_fields| %>
  <%= post_fields.collection_radio_buttons(
        :author_id,     # the foreign key for Authors
        User.limit(5),  # To only show 5 users for example
        :id,
        :name )
  %>
  ...
<% end %>
```

# Nested forms

## Data handling

Must accept data in the following format:

```ruby
{ :user => {
    :name => "foo",
    :email => "foo@bar.com",
    :addresses_attributes => {
      "1" =>  { :street_address => "123 Fake st,
                :city => "Fake City",
                :state => "FK",
                :zip => "12345" },
      "2" =>  { :street_address => "456 Fake st,
                :city => "Fake City",
                :state => "FK",
                :zip => "12345" },... } } }
```

User model:

```ruby
# app/models/user.rb
class User < ActiveRecord::Base
  has_many :addresses
  accepts_nested_attributes_for :addresses,
                                :reject_if => :all_blank, # ignore any addresses that are blank
                                :allow_destroy => true

  # require presence of a parent object without
  # validations that break your nested CREATE action
  has_many :posts, inverse_of: :comments
  has_many :comments, inverse_of: :post
end
```

Sanitizing multiple layers of parameters in your controllers:

```ruby
def whitelisted_user_params
  params.
    require(:user).
    permit( :name,
            :email,
            { :addresses_attributes => [
                :street_address,
                :city,
                :state,
                :zip ] } )
end
```
## Form generation

```ruby
# generic fields_for case
fields_for(record_name, record_object = nil, options = {}, &block)

# fields_for a totally independent/unrelated record
<%= fields_for :address, Address.new do |address_fields| %>
  ...
  <%= address_fields.text_field :city %>
  ...
<% end %>

# fields_for nested onto an existing form using an existing association
# ...which only works if `accepts_nested_attributes_for` is set properly on the User model
<%= form_for @user do |user_fields| %>
  ...
  <%= user_fields.fields_for :addresses do |address_fields| %>
    ...
    <%= address_fields.text_field :city %>
    ...
  <% end %>
<% end %>

# A simple "delete" checkbox with label. persistence is checked in case it's a new form for
# this particular Address object the association gave us in which case it doesn't make sense to display a delete option
<% if address_fields.object.persisted? %>
  <%= address_fields.label :_destroy, "Delete Address?" %>
  <%= address_fields.check_box :_destroy %>
<% end %>
```

## Sample nested form

### Model

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

### View

```
# app/views/products/new.html.erb
<h1>New Product</h1>
<%= render "form" %>


# app/views/products/edit.html.erb
<h1>Edit this product</h1>
<%= render "form" %>


# app/views/products/_form.html.erb
<%= form_for @product do |product_fields| %>

  <%= product_fields.label :name %>
  <%= product_fields.text_field :name %>
  <br>
  <%= product_fields.label :description %>
  <%= product_fields.text_area :description %>
  <br>
  <%= product_fields.label :price %>
  <%= product_fields.text_field :price %>

  <h2>
    Select the Categories this product will be sold under
  </h2>

  <%= product_fields.collection_check_boxes :category_ids,
                                            Category.all,
                                            :id,
                                            :name %>

  <%= render partial: "review_fields", locals: {
                                            product: @product,
                                            product_fields: product_fields } %>

  <%= product_fields.submit "Let's Get Rich" %>

<% end %>


# app/views/products/_review_fields.html.erb
<h2> Product Reviews </h2>
  <%= product_fields.fields_for :reviews do |review_fields| %>

  <h3>A reviewer says:</h3>
    <%= review_fields.label :title %>
    <%= review_fields.text_field :title %>
    <br>
    <%= review_fields.label :body %>
    <%= review_fields.text_area :body %>
    <br>
    <%= review_fields.label :source %>
    <%= review_fields.text_field :source %>

    # This is for the checkbox to destroy the object
    <% if review_fields.object.persisted? %>
      <%= review_fields.label :_destroy, "Delete this review?" %>
      <%= review_fields.check_box :_destroy %>
  <% end %>

<% end %>
```

### Controller

```ruby
# app/controllers/products_controller.rb
class ProductsController < ApplicationController
  def index
    @products = Product.all
  end

  def new
    @product = Product.new
    @review = @product.reviews.build
  end

  def create
    @product = Product.new(whitelisted_params)
    if @product.save
      redirect_to action: :index
    else
      render :new
    end
  end

  def edit
    @product = Product.find(params[:id])
    @product.reviews.build
  end

  def update
    @product = Product.find(params[:id])

    if @product.update(whitelisted_params)
      redirect_to action: :index
    else
      render :edit
    end
  end

  private

  def whitelisted_params
    params.require(:product).permit(:name, :description, :price,
                                    :category_ids => [],
                                    :reviews_attributes =>
                                    [:title, :body, :source, :id,
                                                        :_destroy ])
  end
end
```
