# Borsch Skeleton Documentation

## About Borsch Skeleton

Sometimes, you don't need an overkill solution like [Laravel](https://laravel.com/) or [Symfony](https://symfony.com/).

Borsch is a simple and efficient [PSR-15](https://www.php-fig.org/psr/psr-15) micro framework made to kick-start your
web app or API development by using the tools you prefer, provides a DDD file structure and facilities to ease your
development.

It natively features :

* [Dependency Injection Container](https://github.com/borschphp/borsch-container)
* [Router](https://github.com/borschphp/borsch-router)
* [Request Handlers and Middlewares](https://github.com/borschphp/borsch-requesthandler)
* [Environment Variables](https://github.com/vlucas/phpdotenv)
* [Error Handling](https://github.com/borschphp/borsch-skeleton/blob/master/src/Middleware/ErrorHandlerMiddleware.php)
* [Listeners](https://github.com/borschphp/borsch-skeleton/blob/master/src/Listener/MonologListener.php)

Can be enriched with :

* ORM
* Templating
* Anything else you want

The framework is built around a [PSR-11](https://www.php-fig.org/psr/psr-11/) Container, therefore everything is made around interfaces.  
If something is not at your taste, you can implement your own logic without having to modify everything.

## Contribution Guidelines

Pull requests, bug reports, and all other forms of contribution are welcomed and highly encouraged!

### Submitting Pull Requests

We **love** pull requests!  
Before forking the repo and creating a pull request for non-trivial changes, it is usually best to first open an issue to
discuss the changes, or discuss your intended approach for solving the problem in the comments for an existing issue.

For most contributions, after your first pull request is accepted and merged, you will be invited to the project and given **push access**.

> [!INFO]
> All contributions will be licensed under the project's license.
