# Overview

You can define your application's route in the file `./config/routes.php`.

> [!INFO]
> It uses a [nikic/FastRoute](https://github.com/nikic/FastRoute) implementation router, so you can use the `FastRoute`
> patterns to define your app routes.

By default, in a production environment, a cache file is generated in `./storage/cache/routes.cache.php`.  
You can modify this in the `./config/container.php` file, in the `RouterInterface::class` definition.

```php
$container->set(RouterInterface::class, function () {
    $router = new FastRouteRouter();
    if (env('APP_ENV') == 'production') {
        $router->setCacheFile(__DIR__.'/../../storage/smarty/routes.cache.php');
    }

    return $router;
})->cache(true);
```

## Create routes

You can define application routes using methods on the `Borsch\Application\App` instance.  
Named methods have 2 required parameters and 1 optional:

* [REQUIRED] string $path The path to match
* [REQUIRED] string $handler The container entry to your route handler
* [OPTIONAL] string $name The name of the route

Example :

```php
return function (App $app): void {
    $app->get('/', HomeHandler::class, 'home');

    $app->get('/user[/{id:\d+}]', UserGetHandler::class);
    $app->post('/user', UserPostHandler::class);
    $app->put('/user[/{id:\d+}]', UserPutHandler::class);
    $app->delete('/user[/{id:\d+}]', UserDeleteHandler::class);

    // Better, use 1 handler to deal with all needed methods
    $app->match(['GET', 'POST', 'PUT', 'DELETE'], '/order[/{id:\d+}]', OrderHandler::class);
};
```

## Handlers

You need to provide a [PSR-15 Request Handler](https://www.php-fig.org/psr/psr-15/) to each route.  
The Request Handler will be in charge of generating a response for the matched route.

Example for the `/order[/{id:\d+}]` path upper

```php
namespace App\Handler;

use Laminas\Diactoros\Response\JsonResponse;
use Psr\Http\Message\{ResponseInterface,ServerRequestInterface};
use Psr\Http\Server\RequestHandlerInterface;

class OrderHandler implements RequestHandlerInterface
{
    
    protected function getOrders(ServerRequestInterface $request): ResponseInterface
    {
        return new JsonResponse([
            // Orders...
        ]);
    }
    
    protected function postOrder(ServerRequestInterface $request): ResponseInterface
    {
        // Created order
        return new JsonResponse([]);
    }
    
    protected function putOrder(ServerRequestInterface $request, int $order_id): ResponseInterface
    {
        // Updated order
        return new JsonResponse([]);
    }
    
    protected function deleteOrder(ServerRequestInterface $request, int $order_id): ResponseInterface
    {
        // Deleted order
        return new JsonResponse([]);
    }

    public function handle(ServerRequestInterface $request): ResponseInterface
    {
        $order_id = (int)$request->getAttribute('id');
        
        return match ($request->getMethod()) {
            'GET' => $this->getOrders($request),
            'POST' => $this->createOrder($request),
            'PUT' => $this->updateOrder($request, $order_id),
            'DELETE' => $this->deleteOrder($request, $order_id)
        };
    }
}
```

> [!INFO]
> Borsch skeleton is bundled with [Laminas Diactoros PSR-7](https://github.com/laminas/laminas-diactoros) implementation
> as you can see in this example.

## Names

You can specify a route name explicitly:

```php
// Here "home"
$app->get('/', HomeHandler::class, 'home');
```

Or let the router do it for you:

```php
// Here "GET^/"
$app->get('/', HomeHandler::class, 'home');

// Here "GET:POST^/user[/{id:\d+}]"
$app->match(['GET', 'POST'], '/user[/{id:\d+}]', UserHandler::class);
```

Internally (in Borsch\Router\Route), route names are determined like so:

```php
$this->name = sprintf(
    '%s^%s',
    implode(':', $this->methods),
    $this->path
);
```

> [!WARNING]
> 2 routes cannot have the same name, or an exception will be thrown!

## Groups

To group routes under the same path prefix, use the group method.  
You must provide a `callable`, where the first parameter is an instance of `ApplicationInterface`.

```php
$this->app->group('/grouped/path', function (App $app) {
    $app->group('/to', function (App $app) {
        $app->get('/get', TestHandler::class); // https://example.com/grouped/path/to/get
    });
});
```

## Middlewares

> [!INFO]
> It is not possible to attach middlewares to a route.  
> Instead, use a segregated path in your pipeline.

## Caching

It is possible to cache your route for production use, the Borsch Skeleton already does it for you.  
Please see the container definition for more details if you wish to modify this behavior:

```php
$container->set(RouterInterface::class, function () {
    $router = new FastRouteRouter();
    if (env('APP_ENV') == 'production') {
        // Caching is done here by default
        $router->setCacheFile(cache_path('routes.cache.php'));
    }

    return $router;
})->cache(true);
```