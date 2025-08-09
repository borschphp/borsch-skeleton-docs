# URI Generation

An important feature of the `Borsch\Router\RouterInterface` is its `generateUri()` method.  
This method allows you to generate URIs by providing a route name and optionally an associative array containing
substitutions to be used in the generated URI, particularly for named placeholders (if present).  

## Naming routes

By default, routes use a combination of HTTP methods and path to create a name if none is provided.  
See [the route overview chapter](/routing/overview) seen previously for more details on how to define routes and name
them.

## Generating URIs

Let's imagine you have the route below defined with an optional `id` route variable:

```php
#Route(method: ['GET'], path: '/api/v1/orders[/{id:\d}]', name: 'retrieve.orders');
```

After identifying the URI's name you intend to generate, you can achieve this directly from a `RouterInterface` instance :

```php
$uri_without_param = $router->generateUri('retrieve.orders'); // generates "/api/v1/orders"
$uri_with_param = $router->generateUri('retrieve.orders', ['id' => 42]); // generates "/api/v1/orders/42"
```

If no substitutions are required, you can omit the second argument altogether.
