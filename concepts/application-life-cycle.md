# Application Life Cycle

You'll find the application life cycle in the `public/index.php` file:

```php
<?php

require_once __DIR__.'/../vendor/autoload.php';

use Borsch\RequestHandler\RequestHandlerRunnerInterface;
use Psr\Container\ContainerInterface;

(static function () {
    // Warning, see: https://www.php.net/manual/en/timezones.others.php
    // do not use any of the timezones listed here (besides UTC)
    date_default_timezone_set(env('TIMEZONE', 'UTC'));

    /** @var ContainerInterface $container */
    $container = (require_once __DIR__.'/../config/container.php');

    $runner = $container->get(RequestHandlerRunnerInterface::class);
    $runner->run();
})();
```

## Container

A Dependency Injection Container is used to define **dependencies** (router, request handler, server request factory,
...), middlewares and related.

Most of your application is loaded from the container so its dependencies are automatically resolved.
The **Request Handler Runner** is instantiated, the
[PSR-15 Request Handler](https://www.php-fig.org/psr/psr-15/) is then run to return a
[PSR-7 ResponseInterface](https://www.php-fig.org/psr/psr-7/) to the client.

In the file `./config/container.php` you can find the application dependencies.  
For clarity, the container is filled with definitions in separated files in the `./config/containers` directory.

> [!NOTE]
> The container uses auto-wiring, so most of the time you do not even need to define your dependencies, the container
> will resolve them by itself.

## Pipeline

The pipeline is the heart of your application and consist of a sequence of Middlewares.  
Every request goes through the pipeline until a [PSR-7 ResponseInterface](https://www.php-fig.org/psr/psr-7/) is return.

The default pipeline should be good as is and should not require many changes to comply to your need.  
Your middleware sequence is defined in the `./config/containers/container.handler.php` file, in the
`RequestHandlerInterface::class` definition.

The different steps that occurs in the pipeline :

1. Error handler middleware
2. Problem details handler middleware
3. Trailing slash middleware
4. Content length middleware
5. Routing middleware 
6. Implicit HEAD middleware 
7. Implicit OPTIONS middleware 
8. Method Not Allowed middleware 
9. Body parsing middleware
10. Uploaded files middleware
11. Dispatched middleware 
12. Not Found Handler middleware

> [!NOTE]
> Middlewares are executed in the order they are added into the `RequestHandlerInterface::class` instance (FIFO).

## Routes

Routes are defined in your application handlers via attributes.

For this skeleton to load a route, your handler class must have the `Borsch\Router\Attribute\Controller` attribute, and
your methods must have the `Borsch\Router\Attribute\Route` attribute (or its derivative like
`Borsch\Router\Attribute\Get`, `Borsch\Router\Attribute\Post`, ...).

Note that this skeleton uses a [nikic/FastRoute](https://github.com/nikic/FastRoute) implementation router, so you can
use the FastRoute pattern to define your app routes.

Example of a route definition:

```php
<?php

namespace Application\Handler;

use Borsch\Http\Response\HtmlResponse;
use Borsch\Template\TemplateRendererInterface;
use Borsch\Router\Attribute\{Controller, Get};
use Psr\Http\Message\{ResponseInterface, ServerRequestInterface};
use Psr\Http\Server\RequestHandlerInterface;

#[Controller]
readonly class HomeHandler implements RequestHandlerInterface
{

    public function __construct(
        protected TemplateRendererInterface $engine
    ) {}

    #[Get(path: '/', name: 'home')]
    public function handle(ServerRequestInterface $request): ResponseInterface
    {
        $this->engine->assign([
            'name' => ($request->getQueryParams()['name'] ?? $request->getHeaderLine('X-Name')) ?: 'World'
        ]);

        return new HtmlResponse($this->engine->render('home.tpl'));
    }
}
```

More examples can be found in the `./src/Application/Handler` directory.

### Route cache

In a production environment, 2 cache files are generated in :
* `./storage/cache/loader.routes.cache.php` for the route loader
* `./storage/cache/router.routes.cache.php` for the router

You can modify this in the `./config/containers/container.routes.php` file, in the `RouterInterface::class` definition.

## Response

At the end of the pipeline process, a [PSR-7 ResponseInterface](https://www.php-fig.org/psr/psr-7/) object is returned and used to send back the
response to the client.  
If no route was found, a `404 Not Found` is returned (via the `NotFoundHandlerMiddleware`, see **Pipeline** upper).  
If route was found but with the wrong HTTP Method, a `405 Method Not Allowed` is returned, with an `Allow` header, (via
the `MethodNotAllowedMiddleware`, see **Pipeline** upper).

Your handler methods must return a [PSR-7 ResponseInterface](https://www.php-fig.org/psr/psr-7/) object.
You have several options to return a response:

* Use a `ResponseFactoryInterface` to create a response object
* Use a `ResponseInterface` implementation directly

You can request a Response factory in your handler constructor, or use the `ResponseFactoryInterface` from the
`Borsch\Http\Factory` namespace.  
You can also use the `Borsch\Http\Response` namespace to return a response object directly :

```php
return new Borsch\Http\Response\JsonResponse(['message' => 'Hello World']);
```
