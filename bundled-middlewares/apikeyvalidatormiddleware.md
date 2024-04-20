# ApiKeyValidatorMiddleware

This middleware is here to demonstrate how you can implement a simple authentication middleware in Borsch Framework.  
It assumes that you have an API available at the `/api` path of your application.

Let say to access the API, your users need an access key.It would not be really efficient to check the API Key in every
route call, example:

```php
$app->match(['GET', 'POST', 'PUT', 'DELETE'], '/api/cart[/{id:\d+}]', CartHandler::class); // Check in this handler
$app->match(['GET', 'POST', 'PUT', 'DELETE'], '/api/order[/{id:\d+}]', OrderHandler::class); // In this handler too
$app->match(['GET', 'POST', 'PUT', 'DELETE'], '/api/customer[/{id:\d+}]', CustomerHandler::class); // Here too
```

Instead, we can use a **segregated path** in your pipeline to validate the API Key for every route that match the path
`/api`.

The bundled ApiMiddleware is really simple, and actually let every request pass through it.  
This middleware is simply a proof of concept. _If you do not need it, just remove it from the pipeline_.

Here is a more realistic ApiMiddleware that will check API Key in a database:

```php
class ApiKeyValidatorMiddleware implements MiddlewareInterface
{

    /**
     * @param \PDO $pdo
     */
    public function __construct(
        protected \PDO $pdo
    ) {}

    /**
     * @param ServerRequestInterface $request
     * @param RequestHandlerInterface $handler
     * @return ResponseInterface
     */
    public function process(ServerRequestInterface $request, RequestHandlerInterface $handler): ResponseInterface
    {
        $api_key = $request->getHeaderLine('X-Api-Key');
        
        $statement = $this->pdo->prepare('SELECT `user_id` FROM `users` WHERE `apikey` = ?');
        if (!$statement->execute([$api_key]) || !$statement->columnCount()) {
            return new Response('php://memory', 403);
        }

        $request = $request->withAttribute('user_id', $statement->fetchColumn());

        return $handler->handle($request);
    }
}
```