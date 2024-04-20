# BodyParserMiddleware

This middleware is used to parse the incoming request body into an object or array that can be used letter in your
application.  
You do not need to read the request body and parse it yourself to get the data.

For now, it only processes 3 kind of data type :

* Json
* XML
* URL Encoded

> [!INFO]
> By default, the middleware is segregated to the /api route, but you can use it on any route you'd like.  
> If not needed, remove it from the pipeline.

