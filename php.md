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

# Passwords

```php
<?php

$connection = mysqli_connect($serverAddress, $username, $password, $dbName);
if (!$connection) die('Database connection failed');

if(isset($_POST['submit'])) {
    $username = $_POST['username'];
    $password = $_POST['password'];
    $username = mysqli_real_escape_string($connection, $username);
    $password = mysqli_real_escape_string($connection, $password);
    $password = password_hash($password, PASSWORD_DEFAULT);

    $query = "INSERT INTO users(username, password) VALUES ('$username', '$password')";
    $result = mysqli_query($connection, $query);
    if (!$result) {
        die('Query failed ' . mysqli_error()); 
    } else {
        echo "Record created";
    }
}

?>
```

# Cookies

## Setting cookies

```php
$minute = 60;
$hour = $minute * 60;
$day = $hour * 24;
$year = $day * 365;
$expiration = time() + $year * 2;
setcookie($cookieName, $cookieValue, $expiration);
```

## Reading cookies

```php
$myCookie = $_COOKIE["NameOfCookie"];
if (isset($myCookie)) {
    echo $myCookie;
}
```