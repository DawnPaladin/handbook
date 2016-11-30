# HTML

## Forms

<form action="/posts" method="post" style="width: 200px">
  <label for="title">Title:</label>
  <input type="text" id="title" name="post-title" />
  <label>
    Yes
    <input type="radio" name="post-sfw" value="sfw" />
  </label>
  <label for="post-nsfw">No</label>
    <input type="radio" name="post-sfw" value="nsfw" id="nsfw" />
  </label>
  <input type="text" value="this is already here!">
  <textarea name="body" rows="10" cols="30"></textarea>
  <input id="example-password" type="password">
  <div>
    <input type="radio" name="sex" value="male" checked="true">Male
    <input type="radio" name="sex" value="female">Female
  </div>
  <label>
    <input  type="checkbox"
            name="vehicle"
            value="Bike"
            checked="true">
    I have a bike
  </label>
  <br>
  <label>
    <input  type="checkbox"
            name="vehicle"
            value="Car">
    I have a car    
  </label>
  <label>
    <input  type="checkbox"
            name="flying_carpet"
            disabled="true"
            value="Flying Carpet">
    I have a flying carpet
  </label>
  <input type="submit">
</form>

```html
<form action="/posts" method="post">
  <label for="title">Title:</label>
  <input type="text" id="title" name="post-title" />
  <label>
    Yes
    <input type="radio" name="post-sfw" value="sfw" />
  </label>
  <label for="post-nsfw">No</label>
    <input type="radio" name="post-sfw" value="nsfw" id="nsfw" />
  </label>
  <input type="text" value="this is already here!">
  <textarea name="body" rows="10" cols="30"></textarea>
  <input id="example-password" type="password">
  <input type="radio" name="sex" value="male" checked="true">Male
  <input type="radio" name="sex" value="female">Female
  <label>
    <input  type="checkbox"
            name="vehicle"
            value="Bike"
            checked="true">
    I have a bike
  </label>
  <br>
  <label>
    <input  type="checkbox"
            name="vehicle"
            value="Car">
    I have a car    
  </label>
  <br>
  <label>
    <input  type="checkbox"
            name="flying_carpet"
            disabled="true"
            value="Flying Carpet">
    I have a flying carpet
  </label>
  <input type="submit">
</form>
```
