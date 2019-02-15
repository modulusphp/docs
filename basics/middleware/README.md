# Middleware

## Introduction

Middleware provide a convenient mechanism for filtering HTTP requests entering your application. For example, Modulus includes a middleware that verifies the user of your application is authenticated. If the user is not authenticated, the middleware will redirect the user to the login screen. However, if the user is authenticated, the middleware will allow the request to proceed further into the application.

Of course, additional middleware can be written to perform a variety of tasks besides authentication.

There are several middleware included in the Modulus framework, including middleware for authentication and CSRF protection. All of these middleware are located in the `app/Http/Middleware` directory.

## Defining Middleware

To create a new middleware, use the `make:middleware` Craftsman command:

```
php artisan make:middleware CheckAge
```

This command will place a new `CheckAge` class within your `app/Http/Middleware` directory. In this middleware, we will only allow access to the route if the supplied `age` is greater than 18. Otherwise, we will redirect the users back to the `home` URI.

```php
<?php

namespace App\Http\Middleware;

class CheckAge
{
  /**
   * Handle an incoming request.
   *
   * @param \Modulus\Http\Request $request
   * @param bool $continue
   * @return bool $continue
   */
  public function handle($request, $continue)
  {
    if ($request->age < 18) {
      return redirect('home', 200);
    }

    return $continue;
  }
}
```

As you can see, if the given `age` is less than `18`, the middleware will return an HTTP redirect to the client; otherwise, the request will be passed further into the application. To pass the request deeper into the application (allowing the middleware to "pass"), return $continue.

## Registering Middleware

### Global Middleware

If you want a middleware to run during every HTTP request to your application, list the middleware class in the `$middleware` property of your `app/Http/HttpFoundation.php` class.

### Assigning Middleware To Routes

If you would like to assign middleware to specific routes, you should first assign the middleware a key in your `app/Http/HttpFoundation.php` file. By default, the `$routeMiddleware` property of this class contains entries for the middleware included with Modulus. To add your own, append it to this list and assign it a key of your choosing. For example:

```php
// Within App\Http\HttpFoundation Class...

protected $routeMiddleware = [
  'auth' => \App\Http\Middleware\Authenticate::class,
  'auth.basic' => \App\Http\Middleware\AuthenticateWithBasicAuth::class,
  'auth.api' => \Modulus\Http\Middleware\AuthenticateWithBearerAuth::class,
  'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
];
```

Once the middleware has been defined in the HTTP kernel, you may use the `middleware` method to assign middleware to a route:

```php
Route::get('admin/profile', function () {
  //
})->middleware('auth');
```

You may also assign multiple middleware to the route:

```php
Route::get('/', function () {
  //
})->middleware('first', 'second');
```

### Middleware Groups

Sometimes you may want to group several middleware under a single key to make them easier to assign to routes. You may do this using the `$middlewareGroups` property of your HTTP kernel.

Out of the box, Modulus comes with `web` and `api` middleware groups that contain common middleware you may want to apply to your web UI and API routes:

```php
/**
 * The application's route middleware groups.
 *
 * @var array
 */
protected $middlewareGroups = [
  'web' => [
    \App\Http\Middleware\VerifyCsrfToken::class,
  ],
  'api' => [
    //
  ],
];
```

> {tip} Out of the box, the `web` middleware group is automatically applied to your `routes/web.php` file by the `RouterResolver`.
