# Containers

In the framework, we strongly advocate for the utilization of Dependency Injection (DI) containers, commonly referred to
as DI, for developing your applications. These containers serve several purposes:
* Defining application dependencies (routers, template engines, error handlers, ...)
* Managing middleware and related dependencies.

You can easily define the container entries within the `config/container.php` and/or `config/containers/*.php` files.

Our application skeleton seamlessly integrates dependency configurations, culminating in the creation of our own
container: [Borsch Container](https://github.com/borschphp/borsch-container).  
This container was designed to be fast and easy-to-use, with auto-wiring by default so that you do not have to define
everything, the container is smart enough to instantiate object that it does not know (yet).

When the application is prepared to execute middleware or a handler, it retrieves it from this container.  
This methodology encourages the practice of defining middleware-specific dependencies and implementing factories to
ensure their injection.

To provide flexibility and empower developers to choose their preferred container, our framework typehints against the
PSR-11 Container.  
Which mean you can switch from [Borsch Container](https://github.com/borschphp/borsch-container) to another container
of your liking.

If you prefer using another [PSR-11](https://www.php-fig.org/psr/psr-11/) container but do not want to replace all the
definition already defined by the skeleton, the Borsch container has a method to allow delegation to another
[PSR-11](https://www.php-fig.org/psr/psr-11/) container.  
In the `config/container.php` file, you can set the delegate to another container of your choice, like this:

```php
// ...

$my_container = new MyContainer();
// Set your definitions in $my_container, then delegate it to the Borsch container
$container->delegate($my_container);
```

If a definition is not found in the Borsch container, it will be searched in the delegated container.
