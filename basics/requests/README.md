# HTTP Requests

## Accessing The Request

To obtain an instance of the current HTTP request via dependency injection, you should type-hint the `Modulus\Http\Request` class on your controller method.

```php
<?php

namespace App\Http\Controllers;

use Modulus\Http\Request;

class UserController extends Controller
{
  /**
   * Store a new user
   *
   * @param Request $request
   * @return
   */
  public function store(Request $request)
  {
    $name = $request->input('name');

    //
  }
}
```

#### Dependency Injection & Route Parameters

If your controller method is also expecting input from a route parameter you should list your route parameters after your other dependencies. For example, if your route is defined like so:

```php
Route::put('user/{id}', 'UserController@update');
```

You may still type-hint the `Modulus\Http\Request` and access your route parameter `id` by defining your controller method as follows:

```php
<?php

namespace App\Http\Controllers;

use Modulus\Http\Request;

class UserController extends Controller
{
  /**
   * Update the specified user.
   *
   * @param Request $request
   * @param int $id
   * @return
   */
  public function update(Request $request, int $id)
  {
    //
  }
}
```

#### Accessing The Request Via Route Closures

You may also type-hint the `Modulus\Http\Request` class on a route Closure. Modulus will automatically inject the incoming request into the Closure when it is executed:

```php
use Modulus\Http\Request;

Route::get('/', function (Request $request) {
  //
});
```

#### Methods

You have the following methods available to you:

Name                     | Arguments                  | Returns  | Description
:------------------------|:---------------------------|:---------|:-----------
`add()`                  | `?array $data`             | `array`  | Add items
`has()`                  | `string $name`             | `bool`   | Request has input
`hasFile()`              | `string $name`             | `bool`   | Request has file
`hasCookie()`            | `string $name`             | `bool`   | Request has cookie
`hasHeader()`            | `string $name`             | `bool`   | Request has header
`input()`                | `string $name`             | `string` | Get request input
`file()`                 | `string $name`             | `string` | Get request file
`cookie()`               | `string $name`             | `string` | Get request cookie
`header()`               | `string $name`             | `string` | Get request header
`data()`                 | `null`                     | `array`  | Get request data
`all()`                  | `null`                     | `array`  | Get request data
`files()`                | `null`                     | `array`  | Get request files
`cookies()`              | `null`                     | `array`  | Get request cookies
`headers()`              | `null`                     | `array`  | Get request headers
`method()`               | `null`                     | `string` | Get request method
`isAjax()`               | `null`                     | `bool`   | Request is xmlhttp
`path()`                 | `null`                     | `string` | Get request path
`url()`                  | `null`                     | `string` | Get request url
`route()`                | `null`                     | `object` | Get matched route
`is()`                   | `string $url`              | `bool`   | Request url is $url
`isDownForMaintenance()` | `null`                     | `bool`   | Application is under maintenance or not
`ip()`                   | `null`                     | `string` | Get request IP Address
`validate()`             | `?Closure $closure`        | `mixed`  | Validate request
