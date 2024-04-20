# TrailingSlashMiddleware

In Borsch Framework, this route `/user` is different to this one `/user/`.  
Therefore, it can be useful to redirect URLs ending with `/` to their non-trailing `/` equivalent.

In case you want to add `/` at the end of every URLs, then you can update the middleware to this:

```php
class TrailingSlashMiddleware implements MiddlewareInterface
{

    public function process(ServerRequestInterface $request, RequestHandlerInterface $handler): ResponseInterface
    {
        $uri = $request->getUri();
        $path = $uri->getPath();

        if ($path != '/' && substr($path, -1) != '/') {
            // Permanently redirect paths with a non-trailing slash to their trailing equivalent
            $uri = $uri->withPath($path.'/');

            if ($request->getMethod() == 'GET') {
                return new RedirectResponse((string)$uri, 301);
            }

            $request = $request->withUri($uri);
        }

        return $handler->handle($request);
    }
}
```

If you do not wish to redirect, then remove this middleware from the pipeline.

