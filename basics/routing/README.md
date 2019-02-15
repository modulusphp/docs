# Routing

### Basic Routing

The most basic Modulus routes accept a URI and a Closure, providing a very simple and expressive method of defining routes:

```php
Route::get('foo', function () {
    return 'Hello World';
});
```

##### The Default Route Files

All Modulus routes are defined in your route files, which are located in the `routes` directory. These files are automatically loaded by the framework. The `routes/web.php` file defines routes that are for your web interface. These routes are assigned the web middleware group, which provides the CSRF protection feature.

For most applications, you will begin by defining routes in your `routes/web.php` file. The routes defined in `routes/web.php` may be accessed by entering the defined route's URL in your browser. For example, you may access the following route by navigating to `http://localhost:8000/user` in your browser:

```php
Route::get('/user', 'UserController@index');
```

Routes defined in the `routes/api.php` file are nested within a route group by the `RouterResolver`. Within this group, the `/api` URI prefix is automatically applied so you do not need to manually apply it to every route in the file. You may modify the prefix and other route group options by modifying your `RouterResolver` class.

#### Available Router Methods

The router allows you to register routes that respond to any HTTP verb:

```php
Route::get(string $uri, $callback);
Route::post(string $uri, $callback);
Route::put(string $uri, $callback);
Route::head(string $uri, $callback);
Route::delete(string $uri, $callback);
Route::patch(string $uri, $callback);
Route::options(string $uri, $callback);
Route::any(string $uri, $callback);
Route::if(array $verbs, string $uri, $callback);
Route::redirect(string $uri, string $redirect);
Route::view(string $uri, string $view);
```

You can register a route that responds to multiple HTTP verbs, this is done by using the `if` method. Another cool thing you can do, is register a route that responds to all HTTP verbs using the `any` method. 

```php
Route::if(['POST', 'GET'], '/', function () {
  //
});

Route:::any('/here', function () {
  //
}); 
```

#### CSRF Protection

Any HTML forms pointing to `POST`, `PUT`, or `DELETE` routes that are defined in the `web` routes file should include a CSRF token field. Otherwise, the request will be rejected.:

```php
<form method="POST" action="/profile">
  {% csrf_token %}
  ...
</form>
```

### Redirect Routes

If you are defining a route that redirects to another URI, you may use the `Route::redirect` method. This method provides a convenient shortcut so that you do not have to define a full route or controller for performing a simple redirect:

```php
Route::redirect('/here', '/there');
```

### View Routes

If your route only needs to return a view, you may use the `Route::view` method. Like the `redirect` method, this method provides a simple shortcut so that you do not have to define a full route or controller. The `view` method accepts a URI as its first argument and a view name as its second argument:

```php
Route::view('/welcome', 'welcome');
```

## Route Parameters

You might find yourself in a situation where you would like to have dynamic routes. Maybe you want to load a specific blog post using the blog post id, you can do this by defining a parameter in your route:

```php
Route::get('/blog/{id}', function (int $id) {
  echo 'Blog: ' . $id;
});
```

For a parameter to be matched and passed to the `Closure` or method, it needs to match the name. So if we have the parameter `{name}` in our URI, our `Closure` or method must accept  `string $name`:

```php
Route::get('/{username}/followers/{page}', function (string $username, int $page) {
  //
});
```

## Named Routes

Named routes allow the convenient generation of URLs or redirects for specific routes. You may specify a name for a route by chaining the `name` method onto the route definition:


```php
Route::get('/user/profile', function () {
  //
})->name('profile');
```

You may also specify route names for controller actions:

```php
Route::get('/user/profile', 'UserController@showProfile')->name('profile');
```

#### Generating URLs To Named Routes

Once you have assigned a name to a given route, you may use the route's name when generating URLs or redirects via the global `route` function:

```php
// Generating URLs...
$url = route('profile');

// Generating Redirects...
return redirect()->route('profile');
```

If the named route defines parameters, you may pass the parameters as the second argument to the `route` function. The given parameters will automatically be inserted into the URL in their correct positions:

```php
Route::get('/user/{id}/profile', function (int $id) {
    //
})->name('profile');

$url = route('profile', ['id' => 1]);
```

#### Inspecting The Current Route

If you would like to determine if the current request was routed to a given named route, you may use the `named` method on a Route instance. For example, you may check the current route name from a route middleware:

```php
/**
 * Handle an incoming request.
 *
 * @param \Modulus\Http\Request $request
 * @param bool $continue
 * @return bool $continue
 */
public function handle($request, $continue) : bool
{
  if ($request->route()->name == 'profile') {
    //
  }

  return $continue;
}
```

## Route Groups

Route groups allow you to share route attributes, such as middleware or namespaces, across a large number of routes without needing to define those attributes on each individual route. Shared attributes are specified in an array format as the first parameter to the `Route::group` method.

### Middleware

To assign middleware to all routes within a group, you may add the `middleware` keyword inside the group. Middleware are executed in the order they are listed in the array:

```php
Route::group(['middleware' => ['first', 'second']], function () {
  Route::get('/', function () {
    // Uses first & second Middleware
  });

  Route::get('user/profile', function () {
    // Uses first & second Middleware
  });
});
```

### Route Prefixes

The `prefix` keyword may be used to prefix each route in the group with a given URI. For example, you may want to prefix all route URIs within the group with `admin`:


```php
Route::group(['prefix' => 'admin'], function () {
  Route::get('/users', function () {
    // Matches The "/admin/users" URL
  });
});
```

### Namespaces

Another common use-case for route groups is assigning the same PHP namespace to a group of controllers using the namespace method:

```php
Route::group(['namespace' =>'Admin'], function () {
    // Controllers Within The "App\Http\Controllers\Admin" Namespace
});
```

### Domain Routing

Route groups may also be used to handle domain (or sub-domain) routing. Domains may be assigned route parameters just like route URIs, allowing you to capture a portion of the domain for usage in your route or controller:

```php
Route::group(['domain' => '{account}.example.com'], function() {
  Route::get('user/{id}', function ($account, $id) {
    //
  })
});
```

## Route Model Binding

The Modulus framework provides a convenient way to automatically inject a model instance directly into your routes. Instead of passing a model ID to your method then running a query to retrieve the model that has the model ID that was passed, you can expect (type-hint) a model in your `Closure` or method:

```php
Route::get('/user/{id}', function (App\User $user) {
  //
});
```

```
http://your-site.dev/user/3
```

Because you're passing a `{id}` in your route, and expecting a `$user` model in your `Closure`, the Modulus framework will look for a `App\User` where the `id` is `3`.

#### Query Maps and Groupables

Query maps are Model queries that run before a controller action or `Closure` get's executed. They allow you to have custom Route Model binding rules.

Let's say, you want to retrieve a list of blog posts where the blog title contains the keyword 'drake', you can easily do this by adding the surffix `__like` to the parameter:

```php
Route::get('/blogs/search/{title__like}', function (App\Groupables\Blog $posts) {
  //
});
```

`like` is query map defined in `App\Http\HttpFoundation::$routeModelBinding`.

To create and define your query maps, simply run the Craftsman command:

```
php craftsman craft:querymap <name> <type>
```

##### Help

Type  | Description                             |
-----:|:--------------------------------------- |
model | Create's a new Query Map for models
group | Create's a new Query Map for groupables