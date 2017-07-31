## Introduction

**FireSessions** is a PHP library for managing your project sessions. It lets you store the session data on your local disk, on a Memcached server, or a Redis one; the library can manage 3 types of session variables: user data, flash data and temp data.

## Installation

You can install FireSessions by using [composer](https://getcomposer.org/):

```
composer require kattsoftware/firesessions:^1.0
```

## Configuration
To start using this library, you must set it up:

```php
<?php

require_once 'vendor/autoload.php';

use FireSessions\Session;

$config = array(
    'driver' => Session::FILES_DRIVER,
    'cookie_name' => 'fsessions',
    'save_path' => __DIR__ . DIRECTORY_SEPARATOR . 'sessions',
    'expiration' => 7200,
    'regenerate_time' => 300,
    'destroy_on_regenerate' => false
);

$session = new Session($config);
```

As you can see, the settings are an associative array; they may differ from driver to driver. Below is a list of settings which can be set regardless of the used driver:

| Setting name |  Types accepted   | Default value | Description |
|----------------------------------|---------|------------------------|----------------|
| `cookie_name` | string | `ini_get('session.name')` | The name of the cookie that will be send to user's browser. |
| `expiration` | int |  |         |


## Usage

FireSession implements 3 types of session variables: user data, flash data and temp data.

### User data

_User data_ represents the collection of variables you set during a whole session lifetime. You can use them to store the ID of logged user, the privileges for a specific user, etc. Simply put, they are the classic `$_SESSION` variables.

To **set a user data variable**, you can use the following call:

```php
$session->setUserdata('logged_userid', 1234);
$session->setUserdata('logged_username', 'myUsername');

// or

$session->logged_userid = 1234;
$session->logged_username = 'myUsername';

// you may also set the variable by assigning $_SESSION variable:

$_SESSION['logged_userid'] = 1234;
$_SESSION['logged_username'] = 'myUsername';
```

However, the recommendation here is to use the `$session` variables (the `Session` instance), so you will have consistency all over your project.

You can also set more user data at once:

```php
$session->setUserdata(array(
    'logged_userid' => 1234,
    'logged_username' => 'myUsername'
));
```

To **get a user data variable**, you can use the following call:

```php
echo $session->userdata('logged_userid'); // outputs 1234
echo $session->userdata('logged_username'); // outputs myUsername

// or

echo $session->logged_userid; // outputs 1234
echo $session->logged_username; // outputs myUsername

// or you can use the $_SESSION variable:

echo $_SESSION['logged_userid']; // outputs 1234
echo $_SESSION['logged_username']; // outputs myUsername
```

Once again, the recommendation is to use the `Session` instance. :)

If the requested variable doesn't exists, `userdata()` will return `null`.

If you need **all user data**, you can ask for the whole associative array of user data:

```php
var_dump($session->userdata()); // Outputs all user data
```

To **remove user data**, you can use the following call:

```php
// removes the "logged_userid" variable
$session->unsetUserdata('logged_userid');

// removes multiple user data
$session->unsetUserdata(array('logged_userid', 'logger_username'));
```

### Flash data

Flash data are the same as user data, except their lifetime is the current and the next HTTP request. After that, they will be deleted.

To **set flash data**, you can use the following call:

```php
$session->setFlashdata('success_message', 'Your profile has been saved!');

// setting multiple flash data at once:

$session->setFlashdata(array(
    'firstname_validation' => 'Invalid first name!',
    'email_validation' => 'Invalid e-mail address!'
));
```

To **get flash data** value, you can use the following call:

```php
echo $session->flashdata('email_validation');
// outputs The typed e-mail address is invalid!

// you can also fetch the entire flash data as an associative array:
var_dump($session->flashdata());
```

If, after being redirected to the next request, you want to **keep flash data** for another request, you can use the following call:

```php
$session->keepFlashdata('email_validation');
// Preserves the "email_validation" flash for another request as well

// Preserve more than one flash data for another request
$session->keepFlashdata(array('email_validation', 'firstname_validation'));
```

To **remove flash data**, you can use the following call:

```php
// removes the "email_validation" flash data
$session->unsetFlashdata('email_validation');

// removes multiple flash data
$session->unsetUserdata(array('email_validation', 'firstname_validation'));
```

### Temp data

Temp data are similar to flash data, except they live for a giving number of seconds, instead of current and next request.

To **set temp data**, you can use the following call:

```php
// this will create a temp data 'quiz_score', having the value of 72
// and expiring after 300 seconds
$session->setTempdata('quiz_score', 73, 300);

// setting multiple temp data at once
$session->setFlashdata(array(
    'quiz_question1' => 10,
    'quiz_question2' => 0
), array(
    'quiz_question1' => 300, // quiz_question1 will expire after 300 seconds
    'quiz_question2' => 350 // quiz_question2 will expire after 350 seconds
));

// or you can use the same expiration time for all items:

$session->setFlashdata(array(
    'quiz_question1' => 10,
    'quiz_question2' => 0
), 300);
```

To **get temp data** value, you can use the following call:

```php
echo $session->tempdata('quiz_question1');
// outputs 10

// you can also fetch the entire temp data as an associative array:
var_dump($session->tempdata());
```

`TODO`

## Other

You can also check if certain variables exist:
```php
// all of the below calls return a boolean value
$session->hasUserdata('logged_userid'); 
$session->hasFlashdata('email_validation');
$session->hasTempdata('quizz_answers');
```

If you want to destroy the entire session (which means all types of variables), you can use the following call:

```php
// Deletes the user, flash and temp data + the session cookie
$session->destroy();
```

## Acknowledgments

This library follows the API implementation principles of the [CodeIgniter 3](https://www.codeigniter.com/) Session library, the PHP framework. Big thanks go to their members and contributors!

## License

The library is licensed under the MIT License (MIT). See the `LICENSE` file for more information.

Copyright (c) 2017 KattSoftware dev team.
