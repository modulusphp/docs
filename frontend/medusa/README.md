# Medusa Templates

## Introduction

Medusa is the simple, and easy to use Templating engine provided with the Modulus Framework. Unlike other popular PHP templating engines, Medusa does not restrict you from using plain PHP code or Laravel's `Blade` in your views. In fact, all Medusa views are compiled into plain PHP code and cached until they are modified, meaning Medusa adds essentially zero overhead to your application. Medusa view files use the  `.modulus.php` file extension and are typically stored in the `resources/views` directory.

## Template Inheritance

### Defining A Layout

Two of the primary benefits of using Medusa are template inheritance and sections. To get started, let's take a look at a simple example. First, we will examine a "master" page layout. Since most web applications maintain the same general layout across various pages, it's convenient to define this layout as a single Medusa view:

```twig
<!-- Stored in resources/views/layouts/app.modulus.php -->

<html>
    <head>
        <title>App Name - {% yield('title') %}</title>
    </head>
    <body>
        {% section('sidebar') %}
            This is the master sidebar.
        {% endsection %}

        <div class="container">
            {% yield('content') %}
        </div>
    </body>
</html>
```

As you can see, this file contains typical HTML mark-up. However, take note of the `section` and `yield` directives. The `section` directive, as the name implies, defines a section of content, while the `yield` directive is used to display the contents of a given section.

Now that we have defined a layout for our application, let's define a child page that inherits the layout.

### Extending A Layout

When defining a child view, use the Medusa `using` directive to specify which layout the child view should "inherit". Views which extend a Medusa layout may inject content into the layout's sections using `section` directives. Remember, as seen in the example above, the contents of these sections will be displayed in the layout using `yield`:

```twig
<!-- Stored in resources/views/child.modulus.php -->

{% using('layouts.app') %}

{% section('title') %}
    Page Title
{% endsection %}

{% section('sidebar') %}
    <p>This is appended to the master sidebar.</p>
{% endsection %}

{% section('content') %}
    <p>This is my body content.</p>
{% endsection %}
```

Medusa views may be returned from routes using the global `view` helper:

```php
Route::get('/home', function () {
    return view('child');
});
```

## Displaying Data

You may display data passed to your Medusa views by wrapping the variable in curly braces. For example, given the following route:

```php
Route::get('greeting', function () {
    return view('welcome', ['name' => 'Samantha']);
});
```

You may display the contents of the name variable like so:

```twig
Hello, {{ $name }}.
```

> Medusa {{ }} statements are automatically sent through PHP's htmlspecialchars function to prevent XSS attacks.

You are not limited to displaying the contents of the variables passed to the view. You may also echo the results of any PHP function. In fact, you can put any PHP code you wish inside of a Medusa echo statement:

```twig
The current UNIX timestamp is {{ time() }}.
```

#### Displaying Unescaped Data

By default, Medusa {{ }} statements are automatically sent through PHP's htmlspecialchars function to prevent XSS attacks. If you do not want your data to be escaped, you may use the following syntax:

```twig
Hello, {!! $name !!}.
```

!> Be very careful when echoing content that is supplied by users of your application. Always use the escaped, double curly brace syntax to prevent XSS attacks when displaying user supplied data.

## Control Structures

In addition to template inheritance and displaying data, Medusa also provides convenient shortcuts for common PHP control structures, such as conditional statements and loops. These shortcuts provide a very clean, terse way of working with PHP control structures, while also remaining familiar to their PHP counterparts.

### If Statements

You may construct if statements using the `if`, `elseif`, `else`, and `endif` directives. These directives function identically to their PHP counterparts:

```twig
{% if count($records) === 1 %}
    I have one record!
{% elseif (count($records) > 1 %}
    I have multiple records!
{% else %}
    I don't have any records!
{% endif %}
```

In addition to the conditional directives already discussed, the @isset and @empty directives may be used as convenient shortcuts for their respective PHP functions:

```twig
{% isset($records) %}
    // $records is defined and is not null...
{% endisset %}

{% empty($records) %}
    // $records is "empty"...
{% endempty %}
```

### Loops

In addition to conditional statements, Medusa provides simple directives for working with PHP's loop structures. Again, each of these directives functions identically to their PHP counterparts:

```twig
{% for $i = 0; $i < 10; $i++ %}
    The current value is {{ $i }}
{% endfor %}

{% foreach $users as $user %}
    <p>This is user {{ $user->id }}</p>
{% endforeach %}

{% while true %}
    <p>I'm looping forever.</p>
{% endwhile %}
```

### Comments

Medusa also allows you to define comments in your views. However, unlike HTML comments, Medusa comments are not included in the HTML returned by your application:

```
{-- This comment will not be present in the rendered HTML --}
```

### PHP

In some situations, it's useful to embed PHP code into your views. You can use the Medusa shorthand to execute a block of plain PHP within your template:

```twig
{%
    //
%}
```

## Forms

### CSRF Field

Anytime you define a HTML form in your application, you should include a hidden CSRF token field in the form so that the CSRF protection middleware can validate the request. You may use the `csrf_token` Medusa directive to generate the token field:

```twig
<form method="POST" action="/profile">
    {% csrf_token %}

    ...
</form>
```

## Including Sub-Views

Medusa's `partial` directive allows you to include a Medusa view from within another view. All variables that are available to the parent view will be made available to the included view:

```twig
<div>
    {% partial('shared.errors') %}

    <form>
        <!-- Form Contents -->
    </form>
</div>
```
