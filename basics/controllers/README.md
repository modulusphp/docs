# Controllers

## Introduction

Instead of defining all of your request handling logic as Closures in route files, you may wish to organize this behavior using Controller classes. Controllers can group related request handling logic into a single class. Controllers are stored in the `app/Http/Controllers` directory.

## Basic Controllers

### Defining Controllers

Below is an example of a basic controller class. Note that the controller extends the base controller class included with Modulus.

```php
<?php

namespace App\Http\Controllers;

use App\User;
use App\Http\Controllers\Controller;

class UserController extends Controller
{
  /**
   * Show the profile for the given user.
   *
   * @param  int  $id
   * @return
   */
  public function show(int $id)
  {
    return view('user.profile', ['user' => User::findOrFail($id)]);
  }
}
```

You can define a route to this controller action like so:

```php
Route::get('/user/{id}', 'UserController@show');
```

Now, when a request matches the specified route URI, the `show` method on the `UserController` class will be executed. Of course, the route parameters will also be passed to the method.

### Controllers & Namespaces

It is very important to note that we did not need to specify the full controller namespace when defining the controller route. Since the `RouterResolver` loads your route files within a route group that contains the namespace, we only specified the portion of the class name that comes after the `App\Http\Controllers` portion of the namespace.

```php

protected $namespace = 'App\Http\Controllers';

// ...

```

If you choose to nest your controllers deeper into the `App\Http\Controllers` directory, use the specific class name relative to the `App\Http\Controllers` root namespace. So, if your full controller class is `App\Http\Controllers\Photos\AdminController`, you should register routes to the controller like so:


```php
Route::get('/foo', 'Photos\AdminController@method');
```