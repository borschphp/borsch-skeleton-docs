# Piping

Pipeline configuration is a fundamental aspect of the framework.  
You can easily define the middleware pipeline within the `config/pipeline.php` file.

> [!INFO]
> The middlewares within the pipeline **MUST** be instances of `MiddlewareInterface`.

You have the flexibility to attach middleware to specific paths, enabling a mix-and-match approach for applications
sharing a common domain.  
This is called a **segregated path**.   
It is possible to attach an array of middlewares to a segregated path (they will be resolved in FIFO). In the example
below, the `BodyParserMiddleware` will be invoked before the `UploadedFilesParserMiddleware`.

```php
$app->pipe('/api', ApiKeyValidatorMiddleware::class);
$app->pipe('/api/peoples', [
    BodyParserMiddleware::class,
    UploadedFilesParserMiddleware::class
]);
```