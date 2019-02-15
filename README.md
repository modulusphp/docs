### Server Requirements

The Modulus Framework is backed by the latest version of PHP and a couple of PHP extensions. Here is compiled list of some of the requirements your server must meet for a Modulus Application to run.

* PHP >= 7.3.0
* BCMath or GMP PHP Extension
* Xdebug PHP Extension
* PDO PHP Extension
* Mbstring PHP Extension
* Tokenizer PHP Extension
* XML PHP Extension
* Ctype PHP Extensio
* JSON PHP Extension

### Installing Modulus

Modulus uses <a href="https://getcomposer.org/">Composer</a> for managing dependencies. So, before using Modulus, make sure you have Composer installed on your machine.

#### Craft a new Project

You may install Modulus by issuing the Composer `create-project` command in your terminal:

```
composer create-project --prefer-dist modulusphp/modulusphp api
```

#### Local Development Server

If you have PHP installed locally and you would like to use PHP's built-in development server to serve your application, you may use the `serve` Craftsman command. This command will start a development server at http://localhost:8000:

```
php craftsman serve
```

### Configuration

##### Public Directory

After installing Modulus, you should configure your web server's document / web root to be the `public` directory. The `index.php` in this directory serves as the front controller for all HTTP requests entering your application.

##### Configuration Files

All of the configuration files for the Modulus framework are stored in the config directory. Each option is documented, so feel free to look through the files and get familiar with the options available to you.

##### Directory Permissions

After installing Modulus, you may need to configure some permissions. Directories within the `storage` and the `storage/framework` directories should be writable by your web server or Modulus will not run.

##### Application Key

The next thing you should do after installing Modulus is set your application key to a random string. If you installed Modulus via Composer, this key has already been set for you by the `php craftsman key:generate` command.

Typically, this string should be 32 - 34 characters long. The key can be set in the .env environment file. If you have not renamed the .env.example file to .env, you should do that now. If the application key is not set, JWT tokens will not work.