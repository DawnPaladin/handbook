**PHP**

# Forms

```html
<form action="processForm.php" method="post">
    <label>Name <input type="text" name="name" placeholder="John Smith"></label>
    <label>Comment <textarea name="comment" placeholder="What's up?"></textarea></label>
    <input type="submit" name="submit" />
</form>
```
```php
<?php // processForm.php
    if (isset($_POST['submit'])) {
        echo "It works!";
    }
?>
```

# Databases

```php
<?php
    $connection = mysqli_connect($serverAddress, $username, $password, $dbName);
    if ($connection) {
        echo 'Logged in';
    } else {
        die('Database connection failed');
    }
?>
```