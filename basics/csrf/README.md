# CSRF Protection

## Introduction

Modulus makes it easy to protect your application from [cross-site request forgery](https://en.wikipedia.org/wiki/Cross-site_request_forgery) (CSRF) attacks. Cross-site request forgeries are a type of malicious exploit whereby unauthorized commands are performed on behalf of an authenticated user.

Modulus automatically generates a CSRF "token" for each active user session managed by the application. This token is used to verify that the authenticated user is the one actually making the requests to the application.

Anytime you define a HTML form in your application, you should include a hidden CSRF token field in the form so that the CSRF protection middleware can validate the request. You may use the `@csrf` Blade directive to generate the token field:

```html
<form method="POST" action="/profile">
    {% csrf_token %}
    ...
</form>
```

The `VerifyCsrfToken` [middleware](/docs/2.0/middleware), which is included in the `web` middleware group, will automatically verify that the token in the request input matches the token stored in the session.

#### CSRF Tokens & JavaScript

When building JavaScript driven applications, it is convenient to have your JavaScript HTTP library automatically attach the CSRF token to every outgoing request. By default, the `resources/assets/js/bootstrap.js` file registers the value of the `csrf-token` meta tag with the Axios HTTP library. If you are not using this library, you will need to manually configure this behavior for your application.

<a name="csrf-excluding-uris"></a>
## Excluding URIs From CSRF Protection

It is recommended to place URIs that don't require CSRF protection outside of the `web` middleware group that the `RouterResolver` applies to all routes in the `routes/web.php` file. However, you may also exclude the routes by adding their URIs to the `$except` property of the `VerifyCsrfToken` middleware:

```php
  <?php

  namespace App\Http\Middleware;

  use Modulus\Http\Middleware\VerifyCsrfToken as Middleware;

  class VerifyCsrfToken extends Middleware
  {
    /**
    * The URIs that should be excluded from CSRF verification.
    *
    * @var array
    */
    protected $except = [
      '/there',
    ];
  }
```

## X-CSRF-TOKEN

In addition to checking for the CSRF token as a POST parameter, the `VerifyCsrfToken` middleware will also check for the `X-CSRF-TOKEN` request header. You could, for example, store the token in a HTML `meta` tag:

```html
<meta name="csrf-token" content="{{ csrf_token() }}">
```

Then, once you have created the `meta` tag, you can instruct a library like jQuery to automatically add the token to all request headers. This provides simple, convenient CSRF protection for your AJAX based applications:

```javascript
$.ajaxSetup({
  headers: {
    'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
  }
});
```

> {tip} By default, the `resources/assets/js/bootstrap.js` file registers the value of the `csrf-token` meta tag with the Axios HTTP library. If you are not using this library, you will need to manually configure this behavior for your application.