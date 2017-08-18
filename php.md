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

# File I/O

## Reading

```php
$file = fopen('filename.txt', 'r'); // 'r' for 'read'
echo fread($file, 10); // read first 10 characters
echo fread($file, filesize('filename.txt')); // read entire file
fclose($file);
```

## Writing

```php
$file = fopen('filename.txt', 'w'); // 'w' for 'write'. Warning: entire file will be overwritten.
fwrite($file, 'Write this text to the file');
fclose($file);
```

See the "mode" flags in the [`fopen()` documentation](http://php.net/manual/en/function.fopen.php).
## Erasing

```php
unlink("doomed-file.txt");
```

# Secrets

```php
<?php // includes/db.php

$secretsFile = file_get_contents('../secrets.json');
$secretsJson = json_decode($secretsFile, true);
$dbSecrets = $secretsJson['db'];

$connection = mysqli_connect($dbSecrets['ip'], $dbSecrets['username'], $dbSecrets['password'], $dbSecrets['name']);

if (!$connection) { die("Database connection failed"); }

?>
```

```
// .gitignore
secrets.json
```