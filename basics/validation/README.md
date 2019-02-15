# Validation

## Introduction

Validations in Modulus are handled by the `Request` class. Its recommended to create `Request` classes and adding validation rules instead of using `Modulus\Http\Request`.

## Validation Quickstart

To learn about the `Request` validation features, let's look at a complete example of validating a form and displaying the error messages back to the user.

### Defining The Routes

First, let's assume we have the following routes defined in our `routes/web.php` file:

```php
Route::get('post/create', 'PostController@create');

Route::post('post', 'PostController@store');
```

Of course, the `GET` route will display a form for the user to create a new blog post, while the `POST` route will store the new blog post in the database.

### Creating The Controller

Next, let's take a look at a simple controller that handles these routes. We'll leave the `store` method empty for now:

```php

<?php

namespace App\Http\Controllers;

use Modulus\Http\Request;

class PostController extends Controller
{
  /**
   * Show the form to create a new blog post.
   *
   * @return
   */
  public function create()
  {
    return view('post.create');
  }

  /**
   * Store a new blog post.
   *
   * @param  Request  $request
   * @return Response
   */
  public function store(Request $request)
  {
    // Validate and store the blog post...
  }

  /**
   * Define validation rules
   *
   * @return array
   */
  private function rules() : array
  {
    // Validation rules
  }
}
```

### Writing The Validation Logic

Now we are ready to fill in our `store` method with the logic to validate the new blog post. To do this, we will use the `validate` method provided by the `Modulus\Http\Request` object. If the validation rules pass, your code will keep executing normally; however, if validation fails, an exception will be thrown and the proper error response will automatically be sent back to the user. In the case of a traditional HTTP request, a redirect response will be generated, while a JSON response will be sent for AJAX requests.

To get a better understanding of the `validate` method, let's jump back into the `store` method and the `rules` method:

```php
/**
 * Store a new blog post.
 *
 * @param  Request  $request
 * @return Response
 */
public function store(Request $request)
{
  $request->rules = $this->rules();
  $validatedData = $request->validate();

  // The blog post is valid...
}

/**
 * Define validation rules
 *
 * @return array
 */
private function rules() : array
{
  return [
    'title' => ['required', new Unique('posts'), 'max:255'],
    'body' => 'required',
  ];
}
```

As you can see, we pass the desired validation rules into the `validate` method. Again, if the validation fails, the proper response will automatically be generated. If the validation passes, our controller will continue executing normally.

#### Stopping On First Validation Failure

Sometimes you may wish to stop running validation rules on an attribute after the first validation failure. To do so, assign the `bail` rule to the attribute:

```php
return [
  'title' => ['bail', 'required', new Unique('posts'), 'max:255'],
  'body' => 'required',
];
```

In this example, if the `Unique` rule on the `title` attribute fails, the `max` rule will not be checked. Rules will be validated in the order they are assigned.

#### A Note On Nested Attributes

If your HTTP request contains "nested" parameters, you may specify them in your validation rules using "dot" syntax:

```php
return [
  'title' => ['required', new Unique('posts'), 'max:255'],
  'author.name' => 'required',
  'author.description' => 'required',
];
```

### Displaying The Validation Errors

So, what if the incoming request parameters do not pass the given validation rules? As mentioned previously, Modulus will automatically redirect the user back to their previous location.

Again, notice that we did not have to explicitly bind the error messages to the view in our `GET` route. This is because Modulus will check for errors in the session data, and automatically bind them to the view if they are available. The `$errors` variable will be an instance of `Illuminate\Support\MessageBag`.

So, in our example, the user will be redirected to our controller's `create` method when validation fails, allowing us to display the error messages in the view:

```twig
<!-- /resources/views/post/create.modulus.php -->

<h1>Create Post</h1>

{% if $errors->any() %}
    <div class="alert alert-danger">
        <ul>
            {% foreach $errors->all() as $error %}
                <li>{{ $error }}</li>
            {% endforeach %}
        </ul>
    </div>
{% endif %}

<!-- Create Post Form -->
```