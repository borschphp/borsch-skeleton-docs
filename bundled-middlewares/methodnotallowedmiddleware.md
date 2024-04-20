# MethodNotAllowedMiddleware

When the path matches, but the HTTP method does not, your application should return a `405 Method Not Allowed` status in
response.

This middleware get the `RouteResultInterface` instance from the request attributes.  
If the `RouteResultInterface` represent a method failure, then a `405 Method Not Allowed` is returned, with an `Allow`
header listing the available methods for this route path.