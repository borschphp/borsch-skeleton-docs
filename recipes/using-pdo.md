# Using PDO

If you need a simple connection to a database, you can implement a quick solution using `PDO` then pass it to your
controllers.

## Adding a PDO instance in the container

In your `config/container.php` file, add a new `PDO::class` entry and configure it.  
Example:

```php
/*
 * Database
 * --------
 * This skeleton implements a simple CRUD API that stores and updates a SQLite database via PDO.
 */

$container->set(PDO::class, function () {
    $pdo = new PDO('sqlite:'.storage_path('db.sqlite'));
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    $pdo->setAttribute(PDO::ATTR_DEFAULT_FETCH_MODE, PDO::FETCH_ASSOC);
    $pdo->setAttribute(PDO::ATTR_EMULATE_PREPARES, false);

    return $pdo;
})->cache(true);
```

## Passing the PDO instance to a middleware or request handler

Simply require the `PDO` instance in your handler constructor, the container will take care of instantiating it with the
`PDO` instance.

```php
namespace App\Handler;

use Laminas\Diactoros\Response\JsonResponse;
use PDO;
use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;
use Psr\Http\Server\RequestHandlerInterface;

class OrderHandler implements RequestHandlerInterface
{

    /**
     * OrderHandler constructor.
     *
     * @param PDO $pdo
     */
    public function __construct(
        protected PDO $pdo,
    ) {}

    /**
     * Really basic example, shouldn't be used like this in production...
     *
     * @param ServerRequestInterface $request
     * @return ResponseInterface
     */
    public function handle(ServerRequestInterface $request): ResponseInterface
    {
        $statement = $this->pdo->prepare('SELECT * from `orders`');
        $statement->execute();

        return new JsonResponse($statement->fetchAll());
    }
}
```