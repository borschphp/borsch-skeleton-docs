# ErrorHandlerMiddleware

This middleware should always be the first one in the pipeline.  
Indeed, it set up the different error handlers and catch your application exceptions.

## Listeners

You can add listeners that will be called every time an exception is caught.  
Here is the listener definition:

```php
function myCustomListener(Throwable $throwable, ServerRequestInterface $request) {};
```

You can add your own in `src/Listener` folder then load it/them in the `config/container.php` file, in the
`ErrorHandlerMiddleware::class` definition.

> [!INFO]
> By default, a Monolog listener is added.