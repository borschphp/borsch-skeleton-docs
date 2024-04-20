# Enabling CORS

> Cross-origin resource sharing (CORS) is a mechanism that allows restricted resources on a web page to be requested
> from another domain outside the domain from which the first resource was served.
> > Wikipedia

It is usually recommended to set CORS directly in your web server, but if for some reason you want to deal with CORS in
PHP, here is the solution: **create a new middleware at the beginning of your pipeline**.

## The middleware

Create a new `CorsMiddleware` class in `src/Middleware/CorsMiddleware.php`.
You can do it manually or by using the chef command line : `./chef --middleware CorsMiddleware`

Insert the code below in your new middleware :

```php
namespace App\Middleware;

use Psr\Http\Message\{ResponseInterface, ServerRequestInterface};
use Psr\Http\Server\{MiddlewareInterface, RequestHandlerInterface};

class CorsMiddleware implements MiddlewareInterface
{

    /**
     * @inheritDoc
     */
    public function process(ServerRequestInterface $request, RequestHandlerInterface $handler): ResponseInterface
    {
        return $handler->handle($request)
            ->withHeader('Access-Control-Allow-Origin', '*')
            ->withHeader('Access-Control-Allow-Headers', 'X-Requested-With, Content-Type, Accept, Origin, Authorization')
            ->withHeader('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE, PATCH, OPTIONS');
    }
}
```

## In the timeline

You need to place at the beginning of the timeline:

```php
return function (App $app): void {
    $app->pipe(ErrorHandlerMiddleware::class);

    // Place it here
    $app->pipe(CorsMiddleware::class);

    $app->pipe(TrailingSlashMiddleware::class);
    $app->pipe(ContentLengthMiddleware::class);
    $app->pipe('/api', [ApiMiddleware::class, BodyParserMiddleware::class]);
    $app->pipe(RouteMiddleware::class);
    $app->pipe(ImplicitHeadMiddleware::class);
    $app->pipe(ImplicitOptionsMiddleware::class);
    $app->pipe(MethodNotAllowedMiddleware::class);
    $app->pipe(DispatchMiddleware::class);
    $app->pipe(NotFoundHandlerMiddleware::class);
};
```