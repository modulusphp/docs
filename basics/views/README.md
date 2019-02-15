# Views

## Introduction

Views are typically written in basic HTML and the Modulus Templating Language, provided by Atlantis' powerful `Medusa`. You are not forced to use the default Templating Language, you may also use a basic implementation of `Blade` by changing the default engine in the `views` config file located in the `config` directory.

Views in the Modulus framework use the `.modulus.php` extension and are stored in the `app/resources/views` directory.

> {note} You can change the default extension in the `views` config file.

## Returning A View

Modulus provides a global helper that you can use to call a view. The `view` global helper, accepts the name of the view (without the extension) and a group of variables:

```php

Route::get('/profile/{id}', function (App\User $user) {
  return view('profile', ['user' => $user]);
});

```

You may access the `user` variable in your profile `view` like so:

```html

<h1>This is {{ $user->name }}'s profile</h1>

```

## Caching

All views are cached after they have been loaded for the first time. This means, your application won't compile your views everytime you load them. Cached views are typically stored in the `app/storage/framework/views` directory.

## View routing

You can easily route to views. This eliminates the need to load a Controller or a `closure` before being able to return a view:

```php
Route::view(string $uri, string $view);
```

The `view` method uses the `GET` verb.