# Rails forms

## Building forms

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

## Validation

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
