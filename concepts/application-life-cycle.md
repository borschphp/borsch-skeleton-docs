# Application Life Cycle

You'll find the application life cycle in the `public/index.php` file:

```php
require_once __DIR__.'/../vendor/autoload.php';

use Borsch\Application\ApplicationInterface;
use Psr\Container\ContainerInterface;
use Psr\Http\Message\ServerRequestInterface;

/** @var ContainerInterface $container */
$container = (require_once __DIR__.'/../config/container.php');

$app = $container->get(ApplicationInterface::class);

(require_once __DIR__.'/../config/pipeline.php')($app, $container);
(require_once __DIR__.'/../config/routes.php')($app, $container);

$request = $container->get(ServerRequestInterface::class);

$app->run($request);
```

## Container

A Dependency Injection Container is used to define **application dependencies** (router, request handler, server request
factory and even the Application instance itself), **middlewares** and related dependencies.

The application is loaded from the container so its dependencies are automatically resolved. The pipeline and routes are
then defined.  
The [PSR-15 Request Handler](https://www.php-fig.org/psr/psr-15/) is then run to return a
[PSR-7 ResponseInterface](https://www.php-fig.org/psr/psr-7/) to the client.

In the file `./config/container.php` you can find the application dependencies.  
Usually you will need to add your own dependencies in the Pipeline and Handlers section, but you can add or update
anything else.

## Pipeline

The pipeline is the heart of your application and consist of a sequence of Middlewares.  
Every request goes through the pipeline until a [PSR-7 ResponseInterface](https://www.php-fig.org/psr/psr-7/) is return.

The default pipeline should be good as is and should not require much changes to comply to your need.  
To ease your development, all Pipelines middlewares are located in your `./src/Middleware` folder so feel free to change
everything as you need.

The different steps that occurs in the pipeline :

1. Error handler middleware
2. Trailing slash middleware
3. Segregated path middleware
4. Routing middleware
5. Implicit HEAD middleware
6. Implicit OPTIONS middleware
7. Method Not Allowed middleware
8. Dispatched middleware
9. Not Found Handler middleware

The `./config/pipeline.php` file is well documented, have a look at it if you need more information.

## Routes

You can define your application's routes in the file `./config/routes.php`.  
Note that this skeleton uses a [nikic/FastRoute](https://github.com/nikic/FastRoute) implementation router, so you can
use the FastRoute pattern to define your app routes.

```php
$app->get('/', HomeHandler::class, 'home');
$app->get('/user[/{id:\d+}]', UserHandler::class, 'user');
```

By default, in a production environment, a cache file is generated in `./storage/smarty/routes.cache.php`.  
You can modify this in the `./config/container.php` file, in the `RouterInterface::class` definition.

## Response

At the end of the pipeline process, a [PSR-7 ResponseInterface](https://www.php-fig.org/psr/psr-7/) object is returned
and used to send back the response to the client.  
If no route was found, a `404 Not Found` is returned.  
If route was found but with the wrong HTTP Method, a `405 Method Not Allowed` is returned (with an `Allow` header).

These responses can be modified to your need.