# Installation

## Presentation

Sometimes, you don't need an overkill solution like [Laravel](https://laravel.com/) or [Symfony](https://symfony.com/).

Borsch is a simple and efficient [PSR-15](https://www.php-fig.org/psr/psr-15) micro framework made to kick-start your web app or API development by using the
tools you prefer, and provides minimal structure and facilities to ease your development.

```php
use App\Handler\{HomeHandler, PeoplesHandler};
use Borsch\Application\App;

/**
 * @param App $app
 * @see https://github.com/nikic/FastRoute
 */
return function(App $app): void {
    $app->get('/', HomeHandler::class, 'home');

    $app->match(
        ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
        '/api/peoples[/{id:\d+}]',
        PeoplesHandler::class,
        'peoples'
    );
};
```

#### Approachable

Borsch was designed from the ground up to be easily installed and used to get your app up and running quickly.

#### Fast

Dead simple, Borsch makes every effort possible to simplify configuration and processes resulting in an extra fast experience.

#### Elegant

Builds on top of PHP standard, source code and interfaces have been meticulously written to make you feel at home.

## System requirements

- PHP 8.1 or upper
- Web server
- [Composer](https://getcomposer.org/download/)

## Create a new project

Use the [Composer](https://getcomposer.org/download/)'s create-project command:

```shell
composer create-project borschphp/skeleton my-app-name
```

## Run it

### With built-in PHP server

```shell
php -S 0.0.0.0:8080 -t ./public/ ./public/index.php
```
or
```shell
composer serve
```

### With Docker Compose

```shell
docker-compose up -d
```

### With Lando

```shell
lando start
```

### With FrankenPHP

```shell
docker run \
    -v $PWD:/app \
    -p 80:8080 -p 443:443 -p 443:443/udp \
    dunglas/frankenphp
```

A script is available at `public/worker.php` to run the app with a [FrankenPHP worker](https://frankenphp.dev/docs/worker/):

```shell
docker run \
    -e FRANKENPHP_CONFIG="worker ./public/worker.php" \
    -v $PWD:/app \
    -p 80:8080 -p 443:443 -p 443:443/udp \
    dunglas/frankenphp
```
