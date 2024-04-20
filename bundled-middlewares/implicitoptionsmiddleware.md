# ImplicitOptionsMiddleware

This middleware adds implicit support for `OPTIONS` requests because the HTTP spec recommends servers should support
`OPTIONS` requests and provides support for handling `OPTIONS` requests to routed middleware when the route does not
explicitly allow for the method.

