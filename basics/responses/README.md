# HTTP Responses

## Creating Responses

#### Response Objects

Returning a full `Response` instance allows you to customize the response's HTTP status code and headers:

```php
Route::get('/', function () {
  return response('Hello World')->withHeaders([
    'Content-Type' => 'application/json'
  ]);
});
```

#### Attaching Headers To Responses

You can use the `withHeaders` method to specify an array of headers to be added to the response:

```php
return response($content)
            ->withHeaders([
                'Content-Type' => $type,
                'X-Header-One' => 'Header Value',
                'X-Header-Two' => 'Header Value',
            ]);
```

#### Setting A Status Code

You can specify the status code by using the `code` method:

```php
return response('Look at this...')->code(201);
```

### JSON Responses

The `json` method will automatically set the `Content-Type` header to `application/json`, as well as convert the given array to JSON using the `json_encode` PHP function:

```php
return response()->json([
  'name' => 'Donald Pakkies',
  'age' => 21,
  'city' => 'East Rand',
]);
```

> {optional} add a status code after your first argument to invoke the `send` method, alternatively, you can use `send()` to return the response. 

## Redirects

Redirect responses are instances of the `Modulus\Http\Redirect` class, and contain the proper headers needed to redirect the user to another URL. There are several ways to generate a `Redirect` instance. The simplest method is to use the global `redirect` helper:

```php
Route::get('/dashboard', function () {
  return redirect('home/dashboard');
});
```

Sometimes you may wish to redirect the user to their previous location, such as when a submitted form is invalid. You may do so by using the `back` method provided by the `redirect` helper.

```php
Route::post('/user/profile', function () {
  // something here...

  return redirect()->back();
});
```

### Redirecting To Named Routes

When you call the `redirect` helper with no parameters, an instance of `Modulus\Http\Redirect` is returned, allowing you to call any method on the `Redirect` instance. For example, to generate a `Redirect` to a named route, you may use the `route` method:

```php
return redirect()->route('login');
```

If your route has parameters, you may pass them as the second argument to the `route` method:

```php
// For a route with the following URI: profile/{id}

return redirect()->route('profile', ['id' => 1]);
```

### Redirecting With Temporary Variables

You can redirect to a new URL with data or messages that you may need on the redirected URL for only that moment. You can use the `with` method:

```php
return redirect('/dashboard')->with('status', 'Profile Updated!');
```

After the user is redirected, you can display the temporary variable using the global `Variable` class:

```twig
{% if Variable::has('status') %}
    <div class="alert alert-success">
      {{ Variable::get('status') }}
    </div>
{% endif %}
```
