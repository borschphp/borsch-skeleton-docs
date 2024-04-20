# ImplicitHeadMiddleware

This middleware adds implicit support for `HEAD` requests because the HTTP spec requires servers to support both `GET`
and `HEAD` methods:

> The methods GET and HEAD MUST be supported by all general-purpose servers

By default, Borsch Framework uses a [nikic/FastRoute](https://github.com/nikic/FastRoute) implementation router which
already deals with `HEAD` requests (see here on [nikic/FastRoute page](https://github.com/nikic/FastRoute#a-note-on-head-requests)).

Therefore, this middleware is not really useful because it will never be needed as FastRoute already deals with it.  
The middleware is present in the case you decide to implement your own RouterInterface, so that if it does not deal with
`HEAD` requests, the middleware will do it instead.

> [!INFO]
> It is recommended to keep this middleware in the pipeline, even with the default Borsch Router.