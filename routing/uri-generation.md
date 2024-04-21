# URI Generation

An important feature of the `Borsch\Router\RouterInterface` is its `generateUri()` method.  
This method allows you to generate URIs by providing a route name and optionally an associative array containing
substitutions to be used in the generated URI, particularly for named placeholders (if present).  

## Naming routes

By default, routes use a combination of HTTP methods and path to create a name if none is provided.  
Internally (in Borsch\Router\Route), route names are determined like so:

```php
$this->name = sprintf('%s^%s', implode(':', $this->methods), $this->path);
```

Examples :

```php
$app->get('/foo'); // "GET^/foo"
$app->post('/foo'); // "POST^/foo"
$app->any(['PATCH', 'PUT'], '/foo/{id}'); // PATCH:PUT^/foo/{id}
$app->delete('/foo/{id}'); // DELETE^/foo/{id}
```

Of course, you can provide your own route name :

```php
$app->get('/foo', 'get.foo'); // get.foo
$app->post('/foo', 'post.foo'); // post.foo
$app->any(['PATCH', 'PUT'], '/foo/{id}', 'update.foo'); // update.foo
$app->delete('/foo/{id}', 'delete.foo'); // delete.foo
$app->delete('/home', 'home'); // home
```

## Generating URIs

Let's imagine you have the route below defined in `config/routes.php` with an optional `id` route variable:

```php
$app->get('/api/v1/orders[/{id:\d}]', 'retrieve.orders');
```

After identifying the URI's name you intend to generate, you can achieve this directly from the router instance:

```php
$uri_without_param = $router->generateUri('retrieve.orders'); // "/api/v1/orders"
$uri_with_param = $router->generateUri('retrieve.orders', ['id' => 42]); // "/api/v1/orders/42"
```

If no substitutions are required, you can omit the second argument altogether.