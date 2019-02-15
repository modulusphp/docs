# JavaScript & CSS Scaffolding

## Introduction
While Modulus does not dictate which JavaScript or CSS pre-processors you use, it does provide a basic starting point using [Bootstrap](https://getbootstrap.com/) and [React](https://reactjs.org/) that will be helpful for many applications. By default, Modulus uses [NPM](https://www.npmjs.org/) to install both of these frontend packages.

### CSS

[Laravel Mix](https://laravel.com/docs/5.7/mix) provides a clean, expressive API over compiling SASS or Less, which are extensions of plain CSS that add variables, mixins, and other powerful features that make working with CSS much more enjoyable. In this document, we will briefly discuss CSS compilation in general; however, you should consult the full Laravel Mix documentation for more information on compiling SASS or Less.

### JavaScript

Modulus does not require you to use a specific JavaScript framework or library to build your applications. In fact, you don't have to use JavaScript at all. However, Modulus does include some basic scaffolding to make it easier to get started writing modern JavaScript using the React library.

## Writing CSS

Modulus' `package.json` file includes the `bootstrap` package to help you get started prototyping your application's frontend using Bootstrap. However, feel free to add or remove packages from the `package.json` file as needed for your own application. You are not required to use the Bootstrap framework to build your Modulus application - it is provided as a good starting point for those who choose to use it.

Before compiling your CSS, install your project's frontend dependencies using the [Node package manager (NPM)](https://www.npmjs.org/):

```
npm install
```

Once the dependencies have been installed using `npm install`, you can compile your SASS files to plain CSS using Laravel Mix. The `npm run dev` command will process the instructions in your webpack.mix.js file. Typically, your compiled CSS will be placed in the `public/css` directory:

```
npm run dev
```

The default `webpack.mix.js` included with Modulus will compile the `resources/sass/app.scss` SASS file. This `app.scss` file imports a file of SASS variables and loads Bootstrap, which provides a good starting point for most applications. Feel free to customize the `app.scss` file however you wish.

## Writing JavaScript

All of the JavaScript dependencies required by your application can be found in the  `package.json` file in the project's root directory. This file is similar to a `composer.json` file except it specifies JavaScript dependencies instead of PHP dependencies. You can install these dependencies using the [Node package manager (NPM)](https://www.npmjs.org/):

```
npm install
```

> By default, the Modulus `package.json` file includes a few packages such as `react` and  `axios` to help you get started building your JavaScript application. Feel free to add or remove from the `package.json` file as needed for your own application.

Once the packages are installed, you can use the `npm run dev` command to compile your assets. Webpack is a module bundler for modern JavaScript applications. When you run the  `npm run dev` command, Webpack will execute the instructions in your `webpack.mix.js` file:

```
npm run dev
```

By default, the Modulus `webpack.mix.js` file compiles your SASS and the `resources/js/app.js` file. If you prefer a different framework, configure your own JavaScript application. Your compiled JavaScript will typically be placed in the public/js directory.

> The `app.js` file will load the `resources/js/bootstrap.js` file which bootstraps and configures Axios, jQuery, and all other JavaScript dependencies. If you have additional JavaScript dependencies to configure, you may do so in this file.

## Using Vue

If you prefer to use Vue to build your JavaScript application, Modulus makes it a cinch to swap the React scaffolding with Vue scaffolding. On any fresh Modulus application, you may use the `frontend:switch` command and choose `vue`:

```
php craftsman frontend:switch
```

This single command will remove the React scaffolding and replace it with Vue scaffolding, including an example component.

