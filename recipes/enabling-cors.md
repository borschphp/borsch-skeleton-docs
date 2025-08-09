# Enabling CORS

> Cross-origin resource sharing (CORS) is a mechanism that allows restricted resources on a web page to be requested
> from another domain outside the domain from which the first resource was served.  
> 
> <cite>- Wikipedia</cite>

It is usually recommended to set CORS directly in your web server, but if for some reason you want to deal with CORS in
PHP, here is the solution: **create a new middleware at the beginning of your pipeline**.

## The middleware

Create a new `CorsMiddleware` class in `src/Application/Middleware/CorsMiddleware.php`.
You can do it manually or by using the chef command line : `./chef --middleware CorsMiddleware`

Insert the code below in your new middleware :

```php
namespace Application\Middleware;

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

## The middleware pipeline

You need to place at the beginning of the middleware pipeline, after the `ErrorHandlerMiddleware` and
`ProblemDetailsMiddleware` middlewares.

```php
$container->set(RequestHandlerInterface::class, static function (ContainerInterface $container) {
        return (new RequestHandler())
            ->middleware($container->get(ErrorHandlerMiddleware::class))
            ->middleware($container->get(ProblemDetailsMiddleware::class))
            
            // Here
            ->middleware($container->get(CorsMiddleware::class))
            
            ->middleware($container->get(TrailingSlashMiddleware::class))
            ->middleware($container->get(ContentLengthMiddleware::class))
            ->middleware($container->get(RouteMiddleware::class))
            ->middleware($container->get(ImplicitHeadMiddleware::class))
            ->middleware($container->get(ImplicitOptionsMiddleware::class))
            ->middleware($container->get(MethodNotAllowedMiddleware::class))
            ->middleware($container->get(BodyParserMiddleware::class))
            ->middleware($container->get(UploadedFilesParserMiddleware::class))
            ->middleware($container->get(DispatchMiddleware::class))
            ->middleware($container->get(NotFoundHandlerMiddleware::class));
    });
```
