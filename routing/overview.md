# Overview

Routes are defined in your application handlers via attributes.

For this skeleton to load a route, your handler class must have the `Borsch\Router\Attribute\Controller` attribute, and
your methods must have the `Borsch\Router\Attribute\Route` attribute (or its derivative like
`Borsch\Router\Attribute\Get`, `Borsch\Router\Attribute\Post`, ...).

Note that this skeleton uses a [nikic/FastRoute](https://github.com/nikic/FastRoute) implementation router, so you can
use the FastRoute pattern to define your app routes.

## `Borsch\Router\Attribute\Controller` attribute

This attribute is used to mark a class as a controller, which means it can contain route definitions.  
It must be used on a class that implements `Psr\Http\Server\RequestHandlerInterface`.

It can take an optional `base_path` parameter, which will be used as the base path for all routes defined in the class.  
Example for an `/api/albums` base path:

```php
<?php

namespace Application\Handler;

use Borsch\Router\Attribute\Controller;
use Psr\Http\Server\RequestHandlerInterface;

#[Controller('/api/albums')]
readonly class AlbumHandler implements RequestHandlerInterface
{
    // ...
}
```

Every route defined in this class will have `/api/albums` as a prefix.

## `Borsch\Router\Attribute\Route` attribute

This attribute is used to define a route and must be set on a method.  
One method can have many route attributes, allowing it to handle multiple HTTP methods or paths.

It has one required parameter `methods`, and several optional like `path`, and `name`.

Example for a `GET /api/albums` route using the base path we saw above:

```php
<?php

namespace Application\Handler;

use Borsch\Http\Response\{EmptyResponse, JsonResponse};
use Borsch\Router\Attribute\{Controller, Route};
use Psr\Http\Message\{ResponseInterface, ServerRequestInterface};
use Psr\Http\Server\RequestHandlerInterface;

#[Controller('/api/albums')]
readonly class AlbumHandler implements RequestHandlerInterface
{

    // ...

    #[Route(methods: ['GET'], name: 'albums.list')]
    public function handle(ServerRequestInterface $request): ResponseInterface
    {
        return match ($request->getMethod()) {
            'GET' => $this->getAlbums(),
            default => new EmptyResponse(405)
        };
    }

    private function getAlbums(): ResponseInterface
    {
        return new JsonResponse(
            $this->service->all()
        );
    }

}
```

As you can see in the example above, the `handle` method can handle multiple HTTP methods, it is possible to define 
multiple `Route` attributes on the same method, each with a different `methods` parameter.

Usually, all routes are defined in the `handle` method, so that you can add OpenAPI specification annotations on the
other methods that will handle the request, like `getAlbums` in the example above.  
It allows you to keep your code clean and organized, while still being able to define multiple routes in the same class.

### Different route attributes

The `Route` attribute is a base attribute that can be used to define a route with any HTTP method.  
It has several derivatives that can be used to define routes for specific HTTP methods:

- `Borsch\Router\Attribute\Get`: for `GET` requests
- `Borsch\Router\Attribute\Post`: for `POST` requests
- `Borsch\Router\Attribute\Put`: for `PUT` requests
- `Borsch\Router\Attribute\Patch`: for `PATCH` requests
- `Borsch\Router\Attribute\Delete`: for `DELETE` requests

All these attributes have the same parameters as the `Route` attribute, except for the `methods` one.  
Easier and cleaner to use, they are the recommended way to define routes in your application.

Here is the same example as above, but using the `Get` attribute:

```php
<?php

namespace Application\Handler;

use Borsch\Http\Response\{EmptyResponse, JsonResponse};
use Borsch\Router\Attribute\{Controller, Get};
use Psr\Http\Message\{ResponseInterface, ServerRequestInterface};
use Psr\Http\Server\RequestHandlerInterface;

#[Controller('/api/albums')]
readonly class AlbumHandler implements RequestHandlerInterface
{

    // ...

    #[Get(name: 'albums.list')]
    public function handle(ServerRequestInterface $request): ResponseInterface
    {
        return match ($request->getMethod()) {
            'GET' => $this->getAlbums(),
            default => new EmptyResponse(405)
        };
    }

    private function getAlbums(): ResponseInterface
    {
        return new JsonResponse(
            $this->service->all()
        );
    }

}
```

## Names

You can specify a route name explicitly:

```php
// Here "home"
readonly class HomeHandler implements RequestHandlerInterface
{
    #[Route(methods: ['GET'], path: '/', name: 'home')]
    public function handle(ServerRequestInterface $request): ResponseInterface {}
}
```

Or let the router do it for you:

```php
// Here "GET^/"
readonly class HomeHandler implements RequestHandlerInterface
{
    #[Route(methods: ['GET'], path: '/')]
    public function handle(ServerRequestInterface $request): ResponseInterface {}
}

// Here "GET:POST^/user[/{id:\d+}]"
readonly class UserHandler implements RequestHandlerInterface
{
    #[Route(methods: ['GET', 'POST'], path: '/user[/{id:\d+}]')]
    public function handle(ServerRequestInterface $request): ResponseInterface {}
}
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

## Caching

It is possible to cache your route for production use, the Borsch Skeleton already does it for you.  
Please see the `RouterInterface::class` definition in the container, located in
`./config/containers/container.routes.php` file, for more details if you wish to modify this behavior.

It will generate 2 cache files in the `./storage/cache/` directory:
- `loader.routes.cache.php` for the route loader
- `router.routes.cache.php` for the router

The route loader is responsible for loading the routes from your application handlers (via the attributes seen above),
and the router is responsible for matching the routes to the requests.
