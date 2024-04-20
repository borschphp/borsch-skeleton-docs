# RouteMiddleware

This middleware match your route with the request.

It adds a `RouteResultInterface::class` attribute in the request then process to the next middleware in the pipeline.  
The `RouteResultInterface::class` attribute will then be treated by the `DispatchMiddleware` that will return the
response from your route handler, or a `Not Found` if no route matched the request URI.

