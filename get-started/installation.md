# Installation

## Presentation

Sometimes, you don't need an overkill solution like [Laravel](https://laravel.com/) or [Symfony](https://symfony.com/).

Borsch is a simple and efficient [PSR-15](https://www.php-fig.org/psr/psr-15) micro framework made to kick-start your
web app or API development by using the tools you prefer, provides a DDD file structure and facilities to ease your
development.

```php
<?php

namespace Application\Handler;

use Borsch\Http\Response\HtmlResponse;
use Borsch\Template\TemplateRendererInterface;
use Borsch\Router\Attribute\{Controller, Get};
use Psr\Http\Message\{ResponseInterface, ServerRequestInterface};
use Psr\Http\Server\RequestHandlerInterface;

#[Controller]
readonly class HomeHandler implements RequestHandlerInterface
{

    public function __construct(
        protected TemplateRendererInterface $engine
    ) {}

    #[Get(path: '/', name: 'home')]
    public function handle(ServerRequestInterface $request): ResponseInterface
    {
        $this->engine->assign([
            'name' => (
                $request->getQueryParams()['name'] ??
                $request->getHeaderLine('X-Name')
            ) ?: 'World'
        ]);

        return new HtmlResponse($this->engine->render('home.tpl'));
    }
}
```

## System requirements

- PHP 8.2 or upper
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
or
```shell
composer franken
```
