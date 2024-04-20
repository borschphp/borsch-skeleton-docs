# NotFoundMiddleware

When no path matches, your application should return a `404 Not Found` status in response.

This middleware should be the last one in your pipeline.  
If the request reach this middleware then it simply means that absolutely nothing exists to deal with the request.

By default, a simple 404 response is returned, with no content.  
You can modify it to return something more like a message, example:

```php
class NotFoundMiddleware implements MiddlewareInterface
{

    public function process(ServerRequestInterface $request, RequestHandlerInterface $handler): ResponseInterface
    {
        $response = new Response('php://memory', 404);
        $response->getBody()->write('<h1>Woops!</h1><p>Page not found.</p>');

        return $response;
    }
}
```